# Seized – CyberDefenders Writeup

**Category:** Memory Forensics / Incident Response  
**Difficulty:** Medium  
**Tools Used:** Volatility, CyberChef, strings, linux_bash, linux_pstree, linux_netstat, linux_check_syscall, linux_lsmod

---

## Challenge Overview

In this lab, we investigate a compromised Linux system using memory forensics.  
The analysis is performed on a captured memory dump using Volatility, allowing us to recover volatile artifacts such as running processes, executed commands, network connections, and kernel modifications.

---

## Key Findings

### Q1: What is the CentOS version installed on the machine?
**Answer:** CentOS 7.7 (1908)

**How I found it:**  
Kernel version `3.10.0-1062` maps to CentOS 7.7 based on release documentation.

---

### Q2: There is a command containing a strange message in the bash history. Can you read it?
**Answer:**  
`shkCTF{l3ts_st4rt_th3_1nv3st_75cc55476f3dfe1629ac60}`

**How I found it:**  
Extracted using:
```bash
volatility -f dump.mem --profile=LinuxCentOS7_3_10_1062x64 linux_bash
```
Decoded using CyberChef.

---

### Q3: What is the PID of the suspicious process?
**Answer:** 2854

---

### Q4: The attacker downloaded a backdoor. What is the hidden message?
**Answer:**  
`shkCTF{th4t_w4s_4_dumb_b4ckd00r_86033c19e3f39315c00dca}`

---

### Q5: What are the attacker’s IP address and the local port?
**Answer:**  
- Attacker IP: 192.168.49.1  
- Local Port: 12345

---

### Q6: What is the first command executed by the attacker?
**Answer:**  
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```

---

### Q7: After changing the user password, how did the attacker still have access?
**Answer:** SSH authorized_keys persistence

---

### Q8: What is the name of the rootkit used?
**Answer:** sysemptyrect

---

### Q9: The rootkit uses crc65 encryption. What is the key?
**Answer:** 1337tibbartibbar

---

## MITRE ATT&CK Mapping

| Technique | ID | Evidence |
|--------|----|---------|
| Reverse Shell | T1059.004 | Ncat backdoor |
| SSH Authorized Keys | T1098.004 | Persistent access |
| Rootkit | T1014 | Hooked syscalls |
| Obfuscated Payload | T1027 | crc65 encryption |

---
