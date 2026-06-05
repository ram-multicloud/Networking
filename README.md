# Networking

## 1. How Home Internet Works

Your home network is a small, self-contained network managed by a **router**.

```
Internet
   │
[ISP Modem]
   │
[Router]  ←── manages your home network
  ├── WiFi ── Laptop
  ├── WiFi ── Phone
  └── Ethernet ── Desktop
```

- The **router** is your gateway between your home network and the internet
- Every device that connects (via WiFi or Ethernet cable) becomes part of your **local network**


## 2. IP Addresses & Networks

### What is an IP Address?
Every device on a network is assigned a unique **IP address** — its identity on that network.

> 🔑 **Core Principle:** A device can communicate with any other device **only if they are on the same network** (or routed correctly).

### IP Address Format
```
IPv4 Example:  192.168.1.105
               └──┬──┘ └─┬─┘
               Network  Device
```


## 3. Network Packets & Routing

### How Data Travels
When you communicate with any device, your data is broken into **network packets**. Each packet contains:

| Field | Description |
|-------|-------------|
| **Source** | IP address of the sender |
| **Destination** | IP address of the receiver |
| **Data** | The actual payload (part of your message/file) |

### What is a Router?
A **router** is a networking device that forwards packets between different networks.

- Routers use **Route Tables** — a set of rules that determine where each packet should be forwarded next
- Think of it like a postal sorting facility: it reads the destination address and sends the package to the next stop

```
Packet arrives at Router
       │
  [Route Table]
  ┌────┴────────────────┐
  │ 10.0.0.0/8 → eth0  │
  │ 192.168.0.0/16 → wlan0 │
  │ 0.0.0.0/0 → ISP    │
  └─────────────────────┘
       │
Forwarded to correct interface
```


## 4. Key Network Services

### 🌐 DNS — Domain Name System
Networking works with **IP addresses**, not human-readable names. DNS bridges the gap.

```
You type:  google.com
DNS resolves →  142.250.195.78
Browser connects to the IP
```

> DNS = The "phonebook" of the internet. It translates names → IP addresses.


### 📦 DHCP — Dynamic Host Configuration Protocol
When a device joins a network, it needs an IP address. **DHCP** handles this automatically.

**Process:**
1. Device joins network → broadcasts "I need an IP!"
2. DHCP server responds → "Here's your IP: `192.168.1.105`"
3. Device uses that IP for the lease duration

> Without DHCP, every device would need a manually assigned IP (static IP).


## 5. Public vs Private Networks

### Public Network
- Directly reachable from the internet
- Has a **public IP address** assigned by your ISP
- Example: A web server's IP that anyone can connect to

### Private Network
- **Not directly reachable** from the internet
- Uses reserved IP ranges (defined by RFC 1918):

| Range | CIDR | Common Use |
|-------|------|------------|
| `10.0.0.0` – `10.255.255.255` | `10.0.0.0/8` | Enterprise networks |
| `172.16.0.0` – `172.31.255.255` | `172.16.0.0/12` | Docker, VMs |
| `192.168.0.0` – `192.168.255.255` | `192.168.0.0/16` | Home networks |

> ☁️ **Cloud relevance:** When you create a VPC (Virtual Private Cloud) in AWS/Azure/GCP, you choose one of these private ranges for your network.


## 6. NAT — Connecting Private to Internet

Private network devices can't directly reach the internet (they don't have public IPs). **NAT (Network Address Translation)** solves this.

**How NAT works:**
```
Private Device (192.168.1.5)
        │
        │  wants to reach google.com (142.250.x.x)
        ▼
[Router / NAT Device]
        │  translates source IP → your public IP (e.g., 203.0.113.10)
        ▼
     Internet → google.com
        │
     Response comes back to public IP
        │
[Router / NAT Device]
        │  translates destination back → 192.168.1.5
        ▼
Private Device receives the response
```

**Why NAT matters in Cloud:**
- Cloud VPCs use NAT Gateways so private subnet VMs can reach the internet (for updates, downloads) without being publicly exposed
- Your private EC2/VM instances stay secure behind NAT
