\# 🔐 **PfSense Firewall Security Lab**



**## Project Title**

Design and Implementation of a Secure Virtual Network

Infrastructure using pfSense Firewall with Advanced

Traffic Filtering, Access Control and Threat Prevention



\---



**## 📌 Overview**

Built a complete enterprise-grade network security lab

using pfSense firewall running on VirtualBox with

multiple virtual machines simulating a real company

network environment.



\---



\---



**## 🛠️ Tools and Technologies**

| Tool | Version | Purpose |

|------|---------|---------|

| pfSense | 2.7 | Firewall and Router |

| VirtualBox | 7.0 | Virtualization |

| pfBlockerNG | Latest | IP and DNS Blocking |

| Windows 10 | - | Client Workstation |

| Ubuntu Linux | 22.04 | Server Environment |



\---



* ###### &#x20;**Installation and Initial Setup(Adjustable)**



**2.1 Prerequisites — What You Need**

Requirement		Minimum		Recommended

RAM for pfSense VM	512 MB		1024 MB

Storage for pfSense VM	4 GB		8 GB

RAM for each Client VM	1 GB		2 GB

Your PC Total RAM	8 GB		16 GB

Your PC Storage		50 GB free	100 GB free

VirtualBox Version	6.x		7.x (latest)

pfSense Version		2.6.x		2.7.x (latest)



**2.2 Installing VirtualBox**

&#x20;   1. Go to virtualbox.org and download VirtualBox for your operating system

&#x20;   2. Run the installer and follow all default settings

&#x20;   3. Install VirtualBox Guest Additions when prompted

&#x20;   4. Restart your computer after installation



**2.3 Downloading pfSense**

&#x20;   5. Go to pfsense.org/download

&#x20;   6. Select: Architecture = AMD64 (64-bit)

&#x20;   7. Select: Installer = DVD Image (ISO) Installer

&#x20;   8. Download the .iso.gz file

&#x20;   9. Extract it using 7-Zip or WinRAR to get the .iso file



**2.4 Creating the pfSense Virtual Machine**

Step 1 — Create New VM

&#x20;   10. Open VirtualBox and click New

&#x20;   11. Name: pfSense-Firewall

&#x20;   12. Type: BSD

&#x20;   13. Version: FreeBSD (64-bit)

&#x20;   14. RAM: 1024 MB

&#x20;   15. Create a virtual hard disk: 8 GB, VDI format, Dynamically allocated



**Step 2 — Configure Network Adapters (CRITICAL)**

⚠ This is the most important step. Wrong adapter settings = no internet on client VMs!



&#x20;   16. Click Settings on your pfSense VM

&#x20;   17. Click Network tab



Adapter 1 (WAN — Internet Side):

&#x20;   • Enable Network Adapter: CHECKED

&#x20;   • Attached to: NAT

&#x20;   • This gives pfSense access to your real internet



Adapter 2 (LAN — Client Side):

&#x20;   • Enable Network Adapter: CHECKED

&#x20;   • Attached to: Internal Network

&#x20;   • Name: labnet

&#x20;   • This creates a private network for client VMs



Why NAT for WAN? VirtualBox NAT gives the VM internet access through your PC's connection without needing any configuration. pfSense receives IP 10.0.2.15 automatically from VirtualBox.



**Step 3 — Attach pfSense ISO**

&#x20;   18. Settings > Storage

&#x20;   19. Click the CD icon under Controller: IDE

&#x20;   20. Click Choose Disk File and select your pfSense .iso

&#x20;   21. Click OK and Start the VM



**2.5 pfSense Installation Process**

&#x20;   22. Boot the VM — you will see pfSense boot menu

&#x20;   23. Press Enter to accept default boot option

&#x20;   24. At Copyright screen — press Enter to accept

&#x20;   25. Select Install pfSense

&#x20;   26. Keymap: Select your keyboard layout (usually US default)

&#x20;   27. Partitioning: Select Auto (UFS) — easiest option

&#x20;   28. Wait for installation to complete (2-5 minutes)

&#x20;   29. Select No when asked to open shell

&#x20;   30. Select Reboot

&#x20;   31. After reboot — remove ISO from VirtualBox storage settings



**2.6 Initial Console Configuration**

After pfSense boots, you will see the console menu. This is where we do basic setup before accessing the web interface.



**Option 	Menu Item		When to Use**

0	Logout/Disconnect SSH	End session

1	Assign Interfaces	First time setup — assign WAN/LAN

2	Set Interface IP	Configure LAN IP and DHCP

7	Ping Host		Test internet connectivity

8	Shell			Advanced command line access

9	pfTop			Live traffic monitor

10	Filter Logs		View firewall logs in console

11	Restart GUI		Fix web interface issues

14	Enable SSH		Allow SSH remote access

16	Restart PHP-FPM		Fix web GUI loading issues



Assign Interfaces — Type 1

Enter an option: 1



**Should VLANs be set up now? \[y|n]: n**



Enter the WAN interface name: em0  (or vtnet0)

Enter the LAN interface name: em1  (or vtnet1)



**Do you want to proceed? \[y|n]: y**



Set LAN IP — Type 2

Enter an option: 2

Select interface: 2 (LAN)



Enter the new LAN IPv4 address: 192.168.1.1

Enter the subnet bit count: 24

Press ENTER — no upstream gateway for LAN



Enable DHCP server on LAN? \[y|n]: y

Start address: 192.168.1.100

End address: 192.168.1.200



**Revert to HTTP: y**



###### ***✓ After this step, pfSense web GUI is available at http://192.168.1.1***





**## Accessing the Web GUI**

The Web GUI (Graphical User Interface) is how you manage pfSense. It is a web page hosted by pfSense itself.



1. &#x20;   Start your Client VM (Windows or Linux)
2. &#x20;   Open any web browser
3. &#x20;   Type in address bar: http://192.168.1.1
4. &#x20;   Username: admin
5. &#x20;   Password: pfsense (default — change this after setup!)



**## 🔒 Security Features Implemented**



\### 1. Custom Firewall Rules

\- Blocked SSH (Port 22) — prevent remote access attacks

\- Blocked RDP (Port 3389) — prevent ransomware spread

\- Blocked Telnet (Port 23) — insecure plain text protocol

\- Allow only HTTP/HTTPS traffic



###### **How Firewall Rules Work**

Firewall rules are instructions that tell pfSense what to do with network traffic. Each rule matches specific traffic and either allows it (Pass), blocks it (Block), or rejects it (Reject).



**CRITICAL RULE:** pfSense processes rules TOP to BOTTOM. The FIRST matching rule wins. All traffic that does not match any rule is BLOCKED by default (implicit deny).



* ###### &#x20;Understanding Rule Components

Component		Options	Example

Action			Pass, Block, Reject	Block

Interface		WAN, LAN, any interface	LAN

Address Family		IPv4, IPv6, IPv4+IPv6	IPv4

Protocol		TCP, UDP, ICMP, Any	TCP

Source			Any, LAN net, Single IP, Network	LAN net

Source Port		Any, specific port	Any (clients use random ports)

Destination		Any, Single IP, Network, Alias	Any

Destination Port	Any, HTTP(80), HTTPS(443), SSH(22)	22

Description		Your notes about this rule	Block SSH from LAN

Log			Enable to record matches in logs	Enabled for important rules

State Type		Keep state, Sloppy state, None	Keep state (default)



* ###### &#x20;  Difference: Block vs Reject

Action	What Happens				Attacker Sees				 When to Use

Block	Packet is silently dropped		Connection times out (no response)	 WAN rules — hide that firewall exists

Reject	Packet dropped + error sent back	Connection refused (immediate response)	 LAN rules — tell users they are blocked

Pass	Packet is allowed through		Normal connection			 Allowed traffic





\### 2. pfBlockerNG IP Blocking

\- INBOUND blocking on WAN interface

\- OUTBOUND blocking on LAN interface

\- Threat feeds: Spamhaus, Emerging Threats,

&#x20; Feodo Tracker, Blocklist.de

\- 50,000+ malicious IPs blocked automatically



\### 3. DNSBL Domain Filtering

\- DNS-level malware domain blocking

\- Custom block page displayed to users

\- Feeds: StevenBlack, someonewhocares.org



\### 4. DHCP Server

\- Auto IP assignment (192.168.1.100-200)

\- Static mappings for consistent IPs

\- DORA process configured



\### 5. DNS Resolver

\- Local DNS caching

\- DNSSEC validation enabled

\- Custom host overrides



\### 6. Real-time Monitoring

\- Firewall logs analysis

\- pfTop live traffic monitoring

\---



**## 📸 Screenshots**

\### Dashboard

!\[Dashboard](screenshots/1-pfsense-dashboard.png)



\### Firewall Rules

!\[Rules](screenshots/2-firewall-rules.png)



\### pfBlockerNG Active

!\[pfBlockerNG](screenshots/3-pfblockerng-setup.png)



\### Live Threat Blocking

!\[Alerts](screenshots/4-pfblockerng-alerts.png)



\---



**## 📚 Concepts Demonstrated**

\- Network Segmentation (LAN/WAN)

\- Defense in Depth

\- Stateful Firewall Inspection

\- Threat Intelligence Integration

\- NAT and Port Forwarding

\- DHCP and DNS Management

\- Network Traffic Analysis

\- Security Log Analysis



\---



\## 🎓 Skills Gained

Network Security | Firewall Management | DHCP

DNS Configuration | NAT | pfBlockerNG | VLANs

Traffic Analysis | Troubleshooting | Linux



\---



\## 📂 Repository Structure

pfsense-firewall-lab/

├── README.md

├── screenshots/     ← All lab screenshots

├── docs/           ← Network diagrams, guides

└── configs/        ← Exported firewall rules



\---



\## 🔗 References

\- pfSense Documentation: docs.netgate.com

\- pfBlockerNG Guide: docs.netgate.com/pfsense

