# 🔐 pfSense Firewall Security Lab

## Project Title

**Design and Implementation of a Secure Virtual Network Infrastructure Using pfSense Firewall with Advanced Traffic Filtering, Access Control, and Threat Prevention**

---

## 📌 Project Overview

This project demonstrates the deployment of an enterprise-grade virtual network security lab using **pfSense Firewall** within a **VirtualBox** environment. Multiple virtual machines were configured to simulate a real-world corporate infrastructure, enabling secure network segmentation, traffic monitoring, access control, and automated threat prevention.

The lab focuses on practical cybersecurity concepts such as:

- Stateful firewall configuration
- Threat intelligence integration
- DNS and DHCP management
- Network segmentation
- Real-time traffic monitoring
- Malware and malicious IP blocking

---

## 🛠️ Tools & Technologies

| Tool / Technology | Version | Purpose |
|---|---|---|
| pfSense | 2.7 | Firewall & Router |
| VirtualBox | 7.0 | Virtualization Platform |
| pfBlockerNG | Latest | IP & DNS Threat Blocking |
| Windows 10 | — | Client Workstation |
| Ubuntu Linux | 22.04 | Server Environment |

---

# ⚙️ Installation & Initial Setup

## 2.1 Prerequisites

| Requirement | Minimum | Recommended |
|---|---|---|
| RAM for pfSense VM | 512 MB | 1024 MB |
| Storage for pfSense VM | 4 GB | 8 GB |
| RAM for Client VMs | 1 GB | 2 GB |
| Host System RAM | 8 GB | 16 GB |
| Free Storage Space | 50 GB | 100 GB |
| VirtualBox Version | 6.x | 7.x (Latest) |
| pfSense Version | 2.6.x | 2.7.x (Latest) |

---

## 2.2 Installing VirtualBox

1. Download VirtualBox from `https://www.virtualbox.org`
2. Run the installer using default settings
3. Install VirtualBox Guest Additions when prompted
4. Restart the host machine after installation

---

## 2.3 Downloading pfSense

1. Visit `https://www.pfsense.org/download`
2. Select:
   - **Architecture:** AMD64 (64-bit)
   - **Installer:** DVD Image (ISO) Installer
3. Download the `.iso.gz` image
4. Extract the archive using 7-Zip or WinRAR to obtain the `.iso` file

---

## 2.4 Creating the pfSense Virtual Machine

### Step 1 — Create a New VM

1. Open VirtualBox and click **New**
2. Configure the following:
   - **Name:** `pfSense-Firewall`
   - **Type:** BSD
   - **Version:** FreeBSD (64-bit)
   - **Memory:** 1024 MB
3. Create a virtual hard disk:
   - Size: 8 GB
   - Format: VDI
   - Storage Type: Dynamically Allocated

---

### Step 2 — Configure Network Adapters (Critical)

> ⚠️ Incorrect adapter configuration may prevent client VMs from accessing the internet.

#### Adapter 1 — WAN (Internet Interface)

- Enable Network Adapter: **Enabled**
- Attached To: **NAT**

This adapter provides internet connectivity to pfSense through the host machine.

![WAN Adapter Configuration](https://github.com/user-attachments/assets/7edf61be-f63c-4152-9bd3-35ef8785aa55)

---

#### Adapter 2 — LAN (Internal Network)

- Enable Network Adapter: **Enabled**
- Attached To: **Internal Network**
- Network Name: `labnet`

This adapter creates an isolated internal network for client virtual machines.

![LAN Adapter Configuration](https://github.com/user-attachments/assets/e49aaad4-1866-490c-bfa8-9965787019ac)

---

### Why NAT for WAN?

VirtualBox NAT mode automatically provides internet access to the pfSense VM using the host system’s network connection. pfSense typically receives the IP address `10.0.2.15` automatically.

---

### Step 3 — Attach pfSense ISO

1. Open **Settings → Storage**
2. Select the CD icon under **Controller: IDE**
3. Choose **Disk File**
4. Select the downloaded pfSense ISO
5. Start the VM

---

## 2.5 pfSense Installation Process

1. Boot the VM
2. Press **Enter** to accept the default boot option
3. Accept the license agreement
4. Select **Install pfSense**
5. Choose the appropriate keyboard layout
6. Select **Auto (UFS)** partitioning
7. Wait for installation to complete
8. Select **No** when prompted to open a shell
9. Reboot the VM
10. Remove the ISO after reboot

---

## 2.6 Initial Console Configuration

After installation, basic networking is configured from the pfSense console menu.

### Console Menu Options

| Option | Description | Purpose |
|---|---|---|
| 1 | Assign Interfaces | Configure WAN/LAN |
| 2 | Set Interface IP | Configure LAN IP & DHCP |
| 7 | Ping Host | Test Connectivity |
| 8 | Shell | Advanced CLI Access |
| 9 | pfTop | Real-Time Traffic Monitoring |
| 10 | Filter Logs | View Firewall Logs |
| 11 | Restart GUI | Fix Web Interface Issues |
| 14 | Enable SSH | Allow Remote SSH Access |

---

### Assigning Interfaces

```bash
Enter an option: 1
Should VLANs be set up now? [y|n]: n
Enter the WAN interface name: em0
Enter the LAN interface name: em1
Proceed? [y|n]: y
```

---

### Configuring LAN IP Address

```bash
Enter an option: 2
Select interface: 2 (LAN)

LAN IPv4 Address: 192.168.1.1
Subnet Bit Count: 24
Enable DHCP Server: y

DHCP Range Start: 192.168.1.100
DHCP Range End: 192.168.1.200
```

![DHCP Configuration](https://github.com/user-attachments/assets/e15c7726-fbcd-41d5-bf87-228f8fae5730)

---

### Web GUI Access

After configuration, the pfSense web interface becomes available at:

```bash
http://192.168.1.1
```

---

# 🌐 Accessing the Web GUI

1. Start the client VM
2. Open a web browser
3. Navigate to:

```bash
http://192.168.1.1
```

### Default Credentials

| Username | Password |
|---|---|
| admin | pfsense |

> ⚠️ Change the default password immediately after setup.

![pfSense Dashboard](https://github.com/user-attachments/assets/0db1a0a5-ffcd-441a-8b60-18e27dff67fa)

---

# 🔒 Security Features Implemented

## 1. Custom Firewall Rules

Implemented strict access control policies to reduce attack exposure.

### Configured Rules

- Blocked SSH (Port 22)
- Blocked RDP (Port 3389)
- Blocked Telnet (Port 23)
- Allowed only HTTP/HTTPS traffic

---

### Firewall Rule Processing

pfSense evaluates firewall rules **top to bottom**.  
The **first matching rule** is applied.

Any traffic not explicitly allowed is automatically denied through the **implicit deny rule**.

---

### Firewall Rule Components

| Component | Example |
|---|---|
| Action | Pass / Block / Reject |
| Interface | LAN / WAN |
| Protocol | TCP / UDP / ICMP |
| Source | LAN Net |
| Destination | Any |
| Destination Port | 22 / 80 / 443 |
| Logging | Enabled |

![Firewall Rules](https://github.com/user-attachments/assets/53ee103c-c7f4-4587-8383-c9f2811d2e30)

---

### Block vs Reject

| Action | Behavior | Recommended Usage |
|---|---|---|
| Block | Silently drops traffic | WAN Rules |
| Reject | Sends denial response | LAN Rules |
| Pass | Allows traffic | Authorized Connections |

---

### Firewall Aliases

Aliases were used to simplify firewall rule management by grouping multiple IP addresses or ports under a single logical name.

![Firewall Aliases](https://github.com/user-attachments/assets/f267401e-d82d-453b-944a-e92dc7db6c2c)

---

### Blocking IPs Using Aliases

![IP Blocking via Aliases](https://github.com/user-attachments/assets/bf046aa9-3a3e-4948-83ed-83fb7620b600)

---

## 2. pfBlockerNG Threat Protection

Implemented automated threat intelligence blocking using pfBlockerNG.

### Features

- Inbound WAN protection
- Outbound LAN filtering
- Automatic malicious IP blocking
- Integrated threat feeds:
  - Spamhaus
  - Emerging Threats
  - Feodo Tracker
  - Blocklist.de

> Over 50,000 malicious IP addresses were automatically blocked.

---

## 3. DNSBL Domain Filtering

Configured DNS-based blocking to prevent access to malicious domains.

### Implemented Feeds

- StevenBlack
- someonewhocares.org

### Features

- Malware domain blocking
- DNS-level advertisement filtering
- Custom block pages

---

## 4. DHCP Server Configuration

Configured centralized DHCP services for client devices.

### Features

- Automatic IP allocation
- Static DHCP mappings
- DORA process implementation

### DHCP Lease Monitoring

![DHCP Lease](https://github.com/user-attachments/assets/d1989ec4-367c-4a93-a89a-b3d98a252021)

---

## 5. DNS Resolver Configuration

Implemented secure local DNS resolution.

### Features

- Local DNS caching
- DNSSEC validation
- Host overrides

![DNS Resolver](https://github.com/user-attachments/assets/c1ebf96f-aadb-45e5-9c9a-1c35de279db6)

---

## 6. Real-Time Monitoring

Enabled monitoring and logging tools for live traffic analysis.

### Monitoring Tools

- Firewall log analysis
- pfTop traffic monitoring
- Live connection tracking

---

# 📸 Screenshots

## Dashboard

![Dashboard](screenshots/1-pfsense-dashboard.png)

---

## Firewall Rules

![Firewall Rules](screenshots/2-firewall-rules.png)

---

## pfBlockerNG Configuration

![pfBlockerNG](screenshots/3-pfblockerng-setup.png)

---

## Live Threat Blocking

![Threat Alerts](screenshots/4-pfblockerng-alerts.png)

---

# 📚 Concepts Demonstrated

- Network Segmentation (LAN/WAN)
- Stateful Packet Inspection
- Defense in Depth
- Threat Intelligence Integration
- DHCP & DNS Management
- Traffic Analysis
- Security Monitoring & Logging

---

# 🎓 Skills Acquired

- Network Security
- Firewall Administration
- DHCP & DNS Configuration
- NAT & Routing
- pfBlockerNG Management
- Traffic Monitoring
- Linux Administration
- Troubleshooting & Log Analysis

---

# 📂 Repository Structure

```bash
pfsense-firewall-lab/
│
├── README.md
├── screenshots/      # Lab screenshots
├── docs/             # Network diagrams & documentation
└── configs/          # Exported firewall configurations
```

---

# 🔗 References

- pfSense Documentation  
  `https://docs.netgate.com/pfsense`

- pfBlockerNG Documentation  
  `https://docs.netgate.com/pfsense/en/latest/packages/pfblocker.html`
