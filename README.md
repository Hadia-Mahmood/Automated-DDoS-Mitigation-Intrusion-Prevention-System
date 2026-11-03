# Real-Time DDoS Detection & Automated iptables Mitigation System

An automated Network Intrusion Prevention System (IPS) and stress-testing pipeline built with Python and Scapy. The project consists of two core components: a real-time traffic sniffer that monitors packet velocity and dynamically applies firewall bans via Linux `iptables`, and a custom traffic generator to simulate volume-based network attacks (DoS/DDoS).

## 🛠️ System Architecture

1. **Defensive Monitor (`mitigator.py`):** 
   - Captures live IPv4 packets traversing designated network interfaces.
   - Calculates real-time windowed packet-per-second (PPS) rates grouped by source IP address.
   - Dynamically triggers a system command to block offensive IPs via Netfilter (`iptables -A INPUT -s {ip} -j DROP`) if the traffic threshold exceeds **40 PPS**.
2. **Offensive Simulator (`flooder.py`):**
   - Synthesizes and injects high-velocity raw Ethernet (`Ether()`), Internet (`IP()`), and Transmission Control (`TCP()`) protocol frames directly into the data link layer to validate firewall responsive mechanics.

## 🚀 Features

- **Live Packet Analysis:** Deep packet inspection (DPI) at scale using Scapy's socket sniffing API.
- **Kernel-Level Dropping:** Leverages Linux `iptables` natively to minimize computational overhead during active attacks.
- **State Tracking:** Built with thread-safe data structures (`defaultdict` and state sets) to optimize lookups and prevent redundant firewall mutations.

## 📋 Prerequisites

### Operating System
- **Linux** (Required for the mitigation script due to native dependencies on `iptables` and root execution loops).

### Python Dependencies
Install the required network packet manipulation library:
```bash
pip install scapy
```

## 💻 Getting Started

### 1. Running the Monitor (Requires Root Privileges)
The mitigation script alters Linux kernel firewall rules and requires root permissions to open raw sockets:

```bash
sudo python mitigator.py
```

### 2. Running the Attack Simulator
Configure your desired network targets inside the testing script (`TARGET_IP` and `INTERFACE`), then execute the simulation:

```bash
python flooder.py
```

## 📊 Mitigation Flow Example

When an attack profile breaches the established performance threshold, the monitor logs the following execution state in your terminal:

```text
THRESHOLD: 40
Monitoring network traffic...
Blocking IP: 192.168.10.45, packet rate: 68.0
```

You can confirm the block by inspecting your native system rules:
```bash
sudo iptables -L INPUT -v -n
```

## ⚠️ Disclaimer
This toolkit contains traffic-generation scripts intended solely for controlled laboratory testing, security research, and educational purposes. Do not run the traffic generator against infrastructure without explicit written authorization.
