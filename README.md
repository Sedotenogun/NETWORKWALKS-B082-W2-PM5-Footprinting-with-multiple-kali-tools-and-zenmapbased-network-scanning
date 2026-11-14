# NETWORKWALKS-B082-W2-PM5-Footprinting-with-multiple-kali-tools-and-zenmapbased-network-scanning
Week 2 Cybersecurity Internship lab report: passive website footprinting with Kali Linux tools and local network scanning with Zenmap.
# Week 2 Internship Report: Checking Public Clues & Mapping a Local Network

## What This Project Is About
During Week 2, I completed two beginner security exercises:
1. **Looking up public clues about a website** (`networkwalks.com`) using tools on a Linux computer.
2. **Finding all devices connected to a local network** using a visual map tool on a Windows computer.

Think of the first part like checking the outside of a house—looking at its street address, who owns the property, and whether it has a security fence—without ever touching the front door. The second part is like walking around inside your own house to see which phones, laptops, and routers are currently turned on and connected to the Wi-Fi.

---

## Part 1: Looking Up Public Clues (`networkwalks.com`)

For this exercise, I ran six simple commands on my Linux computer to see what information the website `networkwalks.com` leaves out in the open for anyone to read.

### 1. Checking Who Owns the Website Name (`whois`)
* **Command Run:** `whois networkwalks.com`
* **What I did:** Looked up the public registration card for `networkwalks.com`.
* **What I found:**
  * The website name was bought through **GoDaddy** on **November 6, 2019**, and is paid up until **November 6, 2027**.
  * The owner paid for a privacy shield (**Domains By Proxy, LLC** in Arizona, US) so their real name, home address, and personal phone number stay hidden from strangers.
  * The website's files are stored on servers run by **HostGator** (`NS6135.HOSTGATOR.COM` and `NS6136.HOSTGATOR.COM`).
  * The domain is locked so nobody can steal or delete the website name, though an extra anti-tampering seal (called DNSSEC) is currently turned off.
* **Why this matters:** It tells us who hosts the website and shows that the owner properly hid their personal contact details.

### 2. Seeing What Software Built the Website (`whatweb`)
* **Command Run:** `whatweb networkwalks.com`
* **What I did:** Asked a tool to peek at the website's behind-the-scenes setup to see what programs power it.
* **What I found:**
  * Anyone typing the older, unsecured web address (`http://`) is automatically sent to the encrypted, secure address (`https://`).
  * The website runs on a server located in the **United States** at street number (IP address) **`192.232.216.135`**, powered by **Apache** server software.
  * The site was built using **WordPress (version 7.1.4)** and uses a file-download add-on called **WordPress Download Manager (version 3.3.76)**.
  * It also uses common design helpers (**Bootstrap 7.1.4** and **jQuery 3.7.1**) and **Google Tag Manager** to track visitor activity.
  * A public contact email (`info@networkwalks.com`) is visible on the site.
* **Why this matters:** Showing exact version numbers (like version 7.1.4 or 3.3.76) makes it easy for outsiders to check if those older versions have any unfixed bugs.

### 3. Finding the Website's Number Address (`nslookup`)
* **Command Run:** `nslookup networkwalks.com`
* **What I did:** Computers find websites using numbers, not words. I used a lookup tool to turn the name `networkwalks.com` into its numeric address.
* **What I found:**
  * Asking Google's public directory (`8.8.8.8`) showed that `networkwalks.com` lives at the number address **`192.232.216.135`**.
* **Why this matters:** It confirms the exact server location hosting the website.

### 4. Reading the Website's Greeting Note (`curl -I`)
* **Command Run:** `curl -I https://networkwalks.com`
* **What I did:** Instead of loading the full webpage with images and text, I asked the server to send back only its quick "greeting card" (the hidden status note it sends to web browsers).
* **What I found:**
  * The website replied **`200 OK`**, which simply means "I am online and working fine."
  * It uses speed-boosting tools (caching) so pages load faster for visitors.
  * It gives visitors a locked tracking pass (a secure cookie named `__wpdm_client`) that stops outside scripts from snooping on their session.
  * It uses bot-checking tools (like Google reCAPTCHA, Cloudflare, and hCaptcha) to block spam bots.
  * The greeting note also leaves visible links to the website's internal data feed (`/wp-json/`).
* **Why this matters:** Checking these greeting notes shows whether a site protects visitor sessions properly or accidentally shares internal links.

### 5. Checking If a Security Guard Is Watching (`wafw00f`)
* **Command Run:** `wafw00f networkwalks.com`
* **What I did:** Ran a tool that knocks politely on the website's door to see if a digital security guard (a website firewall) is standing watch.
* **What I found:**
  * Within two quick checks, the tool confirmed that `networkwalks.com` is guarded by a firewall called **ModSecurity (SpiderLabs)**.
* **Why this matters:** A firewall automatically blocks suspicious behavior. Knowing a guard is on duty tells security testers that aggressive scans will be stopped right away.

### 6. Checking Mail & Domain Routing Records (`dnsrecon`)
* **Command Run:** `dnsrecon -d networkwalks.com` 
* **What I did:** Pulled up all the behind-the-scenes routing instructions for `networkwalks.com`—such as where emails go and which servers handle traffic.
* **What I found:**
  * Both HostGator servers (`50.87.144.87` and `192.232.216.131`) publicly announce the exact version of their routing software (**`Bind 9.16.23-RH`**).
  * All emails for the website are handled by **`mail.networkwalks.com`** (`192.232.216.135`).
  * The site has an anti-spam rule (`v=spf1 +a +mx +ip4:50.87.144.87 +include:websitewelcome.com ~all`) listing which servers are allowed to send official emails. However, the `~all` setting at the end means fake emails pretending to be from `networkwalks.com` are only flagged as suspicious rather than blocked outright.
  * Eight automatic setup records (`_autodiscover` pointing to `cpanelemaildiscovery.cpanel.net`) show that the owner manages their email accounts using **cPanel**.
* **Why this matters:** These records show how the company handles email and reveal whether fake emails could slip into people's inboxes.

---

## Part 2: Mapping a Local Network with Zenmap

In the second exercise, I switched from checking a public website to scanning a private, local network on my Windows computer. I used **Zenmap**, a visual program that draws a map of every device currently connected to your network.

### What I Did Step by Step
1. **Installed Zenmap:** Downloaded the official installer for Windows and installed Zenmap along with its network helper driver (**Npcap**), which lets Windows listen to local network traffic.
2. **Checked My Computer's Network Address:** Opened the Windows command window and typed `ipconfig /all` to see my computer's local address (such as `10.0.0.4`) and network range (`255.255.255.0`, which covers 256 addresses from `10.0.0.0` to `10.0.0.255`). I also copied my computer's unique hardware ID (MAC address), because Zenmap does not show the hardware ID of the computer running the scan.
3. **Ran a Roll-Call Scan:** Inside Zenmap, I typed in the network range (`10.0.0.0/24`) and ran a **Ping Scan**. This sends a harmless "Who is online?" roll call across the network without poking at open doors or programs on other computers.
4. **Listed Every Active Device:** Zenmap showed every device turned on in my virtual network (such as the main router/gateway, the address-assigning server, my Windows PC, and my Linux machine), along with each device's temporary network number (IP address) and permanent hardware ID (MAC address).
5. **Saved the Visual Map:** Clicked on Zenmap's **Topology** tab, turned on the map key (legend) to understand the icons, and saved the network map as a **PDF file** on my Desktop.

---

## Simple Lessons Learned
* **Don't overshare version numbers:** Website owners should hide exact software version numbers (like WordPress `7.1.4` or `Bind 9.16.23-RH`) so outsiders cannot easily look up known flaws in those versions.
* **Turn on domain tamper protection:** Enabling DNSSEC adds a digital seal that keeps visitors from being tricked into visiting fake copies of the website.
* **Block fake emails strictly:** Changing the email rule from "flag suspicious mail" (`~all`) to "reject all unauthorized mail" (`-all`) protects customers from scam emails pretending to be from the business.
* **Check your own network regularly:** Running simple roll-call scans with tools like Zenmap helps you see every device connected to your network so you can spot unknown devices right away.
