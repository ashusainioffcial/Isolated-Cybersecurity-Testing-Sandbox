# 🔒 Isolated Cybersecurity Testing Sandbox Lab

## 📌 Project Overview
This repository documents the deployment of a fully isolated, dual-node enterprise-grade cybersecurity testing laboratory built on an optimized hypervisor structure. The environment is designed as a secure, sandboxed playground to safely conduct network reconnaissance, vulnerability scanning, and malware analysis without risking exposure to the host system or local production networks.

---

## 🛠️ Infrastructure & Core Architecture
To maximize storage efficiency and preserve primary system drive performance, the entire hypervisor and virtual machine architecture is hosted on a high-capacity dedicated directory (`D:\VirtualBox_VMs`).

* **Hypervisor Engine:** Oracle VirtualBox 7.x
* **Host Operating System:** Windows 11 Enterprise Environment
* **Network Isolation Segment:** Strict Host-Only Network Topology (No Internet Egress)

### 🖥️ Network Nodes Profile

| Node | Operating System | Role | Subnet IP Address |
| :--- | :--- | :--- | :--- |
| **Node A (Attacker)** | Kali Linux (64-Bit Pre-Built) | Penetration Testing & Reconnaissance | `192.168.56.101` |
| **Node B (Target)** | Metasploitable 2 Linux | Intentionally Vulnerable Target Server | `192.168.56.102` |

---

## 🌐 Network Engineering & Driver Optimization
To achieve flawless internal communication and prevent infinite handshake loading loops, the virtual network layout was explicitly optimized to bypass standard hardware emulation layers:

1. **Host-Only Switch Switchboard:** Configured a dedicated virtual network card with the background DHCP Server disabled to ensure control over static IP space allocation.
2. **Paravirtualized Architecture (`virtio-net`):** Implemented high-performance paravirtualized network drivers on the analyst node. This interfaces directly with the host kernel, solving `VERR_INTNET_FLT_IF_NOT_FOUND` virtualization driver locks.
3. **Verified Connection Loop:** Internal diagnostic validation via ICMP network sweeps confirmed full inter-node visibility with a **0% packet loss index**.

---

## 🕵️‍♂️ Key Skills Demonstrated
* Hypervisor Network Engineering & Subnetting
* Windows 11 NDIS6 Virtual Driver Troubleshooting
* Linux Terminal Static Network Configuration (`ifconfig`)
* Secure Sandbox Isolation and Lab Construction

## 🔍 Phase 1: Network Reconnaissance & Vulnerability Assessment
Conducted an aggressive service-version discovery scan using Nmap against target asset `192.168.56.102` to map the network surface perimeter.

### 📋 Critical Vulnerability Assessment Metrics:
* **Total Discovered Open Ports:** 23/24 Ports Active
* **High-Risk Exposed Vectors:** 
  * `Port 21 (FTP)` running `vsftpd 2.3.4` (Known Backdoor Signature)
  * `Port 23 (Telnet)` unencrypted remote console exposure
  * `Ports 512-514 (R-Services)` unauthenticated shell execution risks
  * `Port 80 / 8180 (HTTP)` legacy Apache application footprints

## 💥 Phase 2: Exploit Verification & Post-Exploitation Auditing
Leveraged the Metasploit Framework to automate exploit verification against the exposed `vsftpd 2.3.4` service on Port 21 to evaluate false-positive indices.

### 📋 Execution Metrics & Findings:
* **Exploit Module:** `exploit/unix/ftp/vsftpd_234_backdoor`
* **Payload Tunnel:** Meterpreter Reverse TCP Session Active
* **Access Level Achieved:** Absolute Administrative Dominance (`uid=0 / root`)

### 🔑 Cryptographic Credential Extraction
Successfully exfiltrated the master system account credential structure directly out of the secure `/etc/shadow` database layer, identifying a legacy MD5 hashing algorithm (`$1$`) in production:
```text
root:$1$/avpfBJ1$x0z8w5UF9Iv./DR9E9Lid.:14747:0:99999:7:::
```

### 🦹‍♂️ Post-Exploit Lateral Movement Auditing
Demonstrated how a compromised asset is converted into an internal sniffing post by deploying native network sniffers (`tcpdump`) to capture raw interface packet telemetry data:
```text
11:36:15.536526 IP 192.168.56.102.39801 > 192.168.56.101.4444: P 691703195:691703419(224)
```


### 🖥️ Production Nmap Scan Query:
```bash
nmap -sV -T4 192.168.56.102
```
