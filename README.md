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

### 🖥️ Production Nmap Scan Query:
```bash
nmap -sV -T4 192.168.56.102
```
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

## 🔓 Phase 3: Offline Credential Auditing & Cryptanalysis (John the Ripper)
Demonstrated an offline dictionary attack matrix against the exfiltrated operating system security credentials database to evaluate corporate password complexity indices.

### 📋 Technical Execution Workflow:
1. **Wordlist Decompression:** Extracted the standard enterprise-grade dictionary directory `rockyou.txt` out of the native compressed system archive repository using Unix extraction streams:
   ```bash
   sudo gunzip /usr/share/wordlists/rockyou.txt.gz
   ```
2. **Algorithmic Fingerprinting:** Leveraged the cryptanalysis engine `John the Ripper` to parse the targets. The engine successfully identified the signature `$1$` prefix as a legacy FreeBSD-MD5 cryptographic structure (`md5crypt`).
3. **Dictionary Matrix Injection:** Executed the brute-force processing payload using parallel computing execution blocks:
   ```bash
   john --wordlist=/usr/share/wordlists/rockyou.txt ~/Desktop/hash_linux.txt
   ```

### 🎯 Key Auditing Results:
* **System Cracking Index:** Rapid identification of multiple high-risk system account match parameters within the first 17% of the total wordlist iteration block.
* **Security Vector Risk:** The audit confirmed highly critical password vulnerabilities across administrative (`root`) and operational (`msfadmin`) layers, demonstrating the severe threat of lateral migration vulnerabilities via unpatched legacy protocols (such as open SSH Port 22 or Telnet Port 23 frameworks).

## 🛡️ Phase 4: Perimeter Hardening & Defensive Engineering (Defense-in-Depth)
Implemented a dual-layered security remediation framework comprising a kernel-level network firewall matrix and standalone application-layer parameter hardening to completely eliminate unauthorized access vectors.

### 🧱 1. Network-Layer Hardening (iptables Firewall)
Deployed targeted Netfilter kernel rules to drop backdoor injection attempts on Port 6200 and restrict administrative FTP entry strictly to the authorized analyst workstation subnet, dropping all other rogue traffic matching the signature perimeter:
```bash
sudo iptables -A INPUT -p tcp --dport 6200 -j DROP
sudo iptables -A INPUT -p tcp -s 192.168.56.101 --dport 21 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 21 -j DROP
```

### 📄 2. Application-Layer Remediation & Forensic Analysis
1. **Daemon Architecture Triage:** Conducted an inspection of the master daemon configuration network map (`/etc/inetd.conf`), confirming the primary application loop was operating in an un-wrapped standalone service engine structure rather than a managed sub-daemon mode.
2. **Configuration Hardening:** Flushed volatile runtime memory caches using `killall` processing blocks and patched the master configuration parameters (`/etc/vsftpd.conf`) to enforce explicit standalone listener execution and permanently drop unauthenticated profiling:
   ```text
   listen=YES
   anonymous_enable=NO
   ```

### 🎯 Verification & Defense Validation Indices:
* **Perimeter Audit (Nmap):** Automated network verification sweeps confirmed that high-risk backdoor vectors targeting Port 6200 successfully shifted to a **`filtered/closed`** security state.
* **Authentication Interception:** Live manual connection checks from the analyst node verified that unauthorized access pathways are completely neutralized, returning explicit **`Connection refused`** blocks at the kernel layer.

## 🔒 Phase 5: Unauthenticated Backdoor Remediation (Port 1524 - ingreslock)
Identified and neutralized a high-risk legacy backdoor signature embedded within the core network configuration parameters during a comprehensive architecture audit.

### 📋 Vulnerability Profiling:
* **Vector Discovered:** `ingreslock` service running on Port 1524.
* **Exploit Vector Mechanics:** A hardcoded shell wrapper mapping incoming connections straight to an interactive root command prompt:
  ```text
  ingreslock stream tcp nowait root /bin/bash bash -i
  ```
* **Threat Profile:** Critical severity. Complete authentication bypass leading to immediate, unauthenticated system dominance (`uid=0 / root`).

### 🛠️ Remediation Engineering:
1. **Volatile Process Eviction:** Evicted the master internet daemon service structures out of live memory slots to drop stale execution paths:
   ```bash
   sudo killall -9 inetd
   ```
2. **Configuration File Hardening:** Commented out the structural entry line inside the system configuration map (`/etc/inetd.conf`) to permanently decouple the bash binary script from network listening sockets.

### 🎯 Security Validation Metrics:
* Deployed automated validation checking using raw protocol stream probes (`netcat`), confirming full environment stabilization.
* **Current Operational Threat Index:** Zero. Network sweeps successfully return an absolute **`Connection refused`** status block at the boundary line.

## 👥 Phase 6: Local Identity Access Auditing & PAM Compliance Hardening
Executed local operating system hardening policies to align the target asset with corporate security frameworks (e.g., PCI-DSS / ISO 27001) by enforcing strict credential governance.

### ⏳ 1. Global Password Aging Governance
Audited account expiration records using system query utilities (`chage -l`), identifying critical vulnerabilities where core profiles were set to never expire. Remediated the global baseline framework inside `/etc/login.defs` and forced runtime account compliance:
```text
PASS_MAX_DAYS   90      # Enforced mandatory 90-day expiration cycle
PASS_MIN_DAYS   7       # Prevented rapid-fire credential cycling
```
* **Active Execution:** Applied account policy constraints directly to operational nodes via terminal execution:
  ```bash
  sudo chage -M 90 -W 7 msfadmin
  ```

### 🔐 2. Cryptographic Complexity Enforcement (PAM Injection)
Intercepted the Pluggable Authentication Module (**PAM**) layer inside `/etc/pam.d/common-password` to establish structural limits preventing brute-force dictionary attacks. Appended structural length boundaries to the active authentication directive:
```text
password   requisite   pam_unix.so nullok obscure md5 minlen=12
```

### 🎯 Verification & Defense Validation Indices:
* **Constraint Compliance Test:** Simulated a non-compliant user attempting a short password change modification index (`abcd`). 
* **Current Operational State:** The PAM architecture successfully intercepted the raw string input, threw a hard termination fault reading **`you must choose a longer password`**, and permanently blocked the weak credential transaction.

## 📜 Phase 7: Defensive Automation & Configuration State Archiving (Bash Scripting)
Engineered an automated shell scripting framework to handle routine security snapshots, configuration validation auditing, and state restoration processes for Linux kernel architectures.

### 📋 Automation Architecture (`backup_security.sh`):
* Deployed a production-grade Bash wrapper using secure pipeline redirection utilities (`tee`) to capture volatile kernel configurations directly to disk arrays.
* Built full state preservation matrices to compress, stamp, and archive active firewalls alongside identity access module files (`/etc/pam.d/common-password`).

### 🎯 Verification & State Auditing Indices:
* **Volatility Analysis Audit:** Verified that default hypervisor reboots flush volatile RAM memory environments completely.
* **State Snapshot Test:** Re-injected active perimeters and successfully executed the automated tarball generation matrix.
* **Forensic Archive Readout:** Extracted the `.tar.gz` package payload structures, confirming that the configuration rules are preserved and ready for rapid restoration down to the byte layer.

## 🌐 Phase 8: Web Application Tier Auditing & UNION-Based SQL Injection (SQLi)
Conducted raw string vulnerability assessments against application-layer endpoints to evaluate input sanitization defects and structural logic bypasses.

### 📋 Exploit Verification Metrics:
* **Target Interface:** Damn Vulnerable Web Application (DVWA) - SQL Injection Module
* **Environment Constraints:** Local Sandbox Host-Only Network Subnet (`192.168.56.102`)
* **Vulnerability Class:** Unsanitized Input Field leading to Arbitrary Database Query Execution (OWASP Top 10)

### 🥷 1. Logical Logic Bypass (Tautology Attack)
Injected a boolean absolute truth string payload to force the backend database engine interpreter to bypass user validation and extract full account directories:
```text
1' OR '1'='1
```
* **Result:** Successfully exfiltrated full backend user profiles including First Names and Surnames for the entire system registry database index.

### 🔑 2. Advanced Data Exfiltration (UNION Selection)
Leveraged structural operators (`UNION SELECT`) alongside line-termination parameters (`#`) to hijack the execution stream, bridging backend column mappings directly to the internal security schema tables:
```text
1' UNION SELECT user, password FROM users#
```
* **Exfiltrated Passwords Database Artifacts (MD5 Hashes):**
  * `admin`  :: `5f4dcc3b5aa765d61d8327deb882cf99`
  * `gordonb`:: `e99a18c428cb38d5f260853678922e03`
  * `1337`   :: `8d3533d75ae2c3966d7e0d4fcc69216b`
  * `pablo`  :: `0d107d09f5bbe40cade3de5c71e9e9b7`
  * `smithy` :: `5f4dcc3b5aa765d61d8327deb882cf99`

### 🎯 Forensic Analysis & Security Impact:
The cryptanalysis audit identified cross-account credential reuse patterns, specifically confirming that the `admin` and `smithy` user accounts share identical raw hash footprints. This validates a catastrophic threat surface where an attacker can achieve lateral movement across roles using single compromise factors.

## 🔓 Phase 9: Cryptanalysis Crack Matrix & Secure Source-Code Remediation
Executed a complete full-circle remediation pipeline comprising offline cryptographic database cracking and web-tier software engineering source-code patching (OWASP Top 10 mitigation).

### ⚡ 1. Cryptanalysis Cracking Operations (John the Ripper)
Compiled the exfiltrated 32-character hexadecimal database hashes into a target matrix file and deployed parallel wordlist dictionary attacks. Explicitly targeted the raw database structures using the `raw-md5` format flag:
```bash
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt ~/Desktop/web_hashes.txt
```
* **Decrypted Plaintext Credentials Unmasked:**
  * `admin`   :: `password`
  * `gordonb` :: `abc123`
  * `1337`    :: `charley`
  * `pablo`   :: `letmein`
  * `smithy`  :: `password`
* **Forensic Finding:** Confirmed high-severity cross-role password reuse vulnerability between `admin` and `smithy` identities.

### 🛡️ 2. Secure Code Engineering (SQLi Mitigation Patch)
Analyzed the vulnerable backend PHP application layer, identifying dynamic input concatenation as the root structural flaw causing database logic bypass. Engineered the production-grade mitigation patch using **Parameterized Prepared Statements** to safely isolate executable command logic from literal text inputs:
```php
// Safe Parameterized Execution Blueprint
stmt = conn->prepare("SELECT first_name, last_name FROM users WHERE user_id = ?");
\$stmt->bind_param("s", \(id);\)stmt->execute();
result = stmt->get_result();
```
* **Security Control Context:** Binding the variable strictly as an isolated parameter value forces the SQL interpreter to process input strings literally, permanently neutralizing SQL Injection threats regardless of input characters.

## 🤖 Phase 10: Automated Web Server Vulnerability Assessments (Nikto Analytics)
Deployed automated web application auditing engines to conduct rapid, line-by-line configuration assessments and footprinting against the target web infrastructure layer.

### 📋 Scanning Metrology:
* **Tool Framework:** Nikto Web Server Scanner
* **Execution Payload:** `nikto -h http://192.168.56`
* **Target Environment:** Apache/2.2.8 (Ubuntu) DAV/2 Engine Subnet

### 🚨 Critical Vulnerability Identifiers Discovered:
1. **Critical Information Disclosure (`/phpinfo.php`):** Identified an exposed configuration script exposing internal environment variables, kernel paths, and raw system environment parameters.
2. **Cross-Site Tracking Exposure (XST via HTTP TRACE):** Confirmed the active status of the dangerous `HTTP TRACE` connection method, allowing for potential session cookie exfiltration.
3. **Directory Traversal Risks (Directory Indexing):** Discovered open directory listings on `/icons/` and `/doc/`, allowing unauthenticated browser navigation through internal server file systems.
4. **Compliance Deficiencies (Missing Security Headers):** Flagged a complete absence of mandatory defense headers including `Content-Security-Policy` (CSP), `X-Content-Type-Options`, and `Strict-Transport-Security` (HSTS).
