# DumpMe – CyberDefenders Writeup

**Category:** Memory Forensics / Incident Response  
**Difficulty:** Medium  
**Tools Used:** Volatility 2 (REMnux), YARA, strings

---

## Challenge Overview

In this lab, we analyze a Windows memory dump taken from a system infected with **Meterpreter malware**.  
Using **Volatility**, we extract indicators of compromise (IOCs), identify malicious processes, recover credentials, and analyze memory artifacts to reconstruct attacker activity.

The investigation focuses on:
- Memory image identification
- Process and network analysis
- Malware detection using YARA
- Credential extraction
- VAD and DLL analysis
- User activity reconstruction

---

## Key Findings

### Q1: What is the SHA1 hash of the memory dump?
**Answer:** *(calculated using sha1sum)*

**How I found it:**
```bash
sha1sum Triage-Memory.mem
```

---

### Q2: What is the most appropriate Volatility profile?
**Answer:** Win7SP1x64

**How I found it:**
```bash
vol.py -f Triage-Memory.mem imageinfo
vol.py -f Triage-Memory.mem kdbgscan
```

Kernel and build information matched **Windows 7 SP1 (64-bit)**.

---

### Q3: What was the PID of notepad.exe?
**Answer:** 3032

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 psscan | grep notepad.exe
```

---

### Q4: Name the child process of wscript.exe
**Answer:** *(identified via pstree)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 pstree
```

---

### Q5: What was the IP address of the machine at dump time?
**Answer:** *(Local IPv4 address)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 netscan | grep v4 | egrep -v '0.0.0.0|127.0.0.1'
```

---

### Q6: What is the attacker’s IP address?
**Answer:** *(Identified via Meterpreter connection)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 yarascan -y metasploit.yar
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 netscan | grep <infected_pid>
```

---

### Q7: How many processes are associated with VCRUNTIME140.dll?
**Answer:** *(Counted via dlllist)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 dlllist | grep VCRUNTIME140.dll
```

---

### Q8: What is the MD5 hash of the dumped malicious process?
**Answer:** *(calculated from dumped executable)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 procdump -p <PID> -D .
md5sum executable.<PID>.exe
```

---

### Q9: What is the LM hash of Bob’s account?
**Answer:** *(Extracted from hashdump)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 hashdump | grep -i bob
```

---

### Q10: What memory protection constants does the VAD node have?
**Answer:** *(Identified via vadinfo)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 vadinfo | grep -C 5 0xfffffa800577ba10
```

---

### Q11: What memory protection did the specified VAD range have?
**Answer:** *(Identified via vadinfo)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 vadinfo | egrep -C 3 '0x00000000033c0000|0x00000000033dffff'
```

---

### Q12: What is the name of the VBS script executed?
**Answer:** *(Script name without extension)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 cmdline | grep vbs
```

---

### Q13: What program ran at 2019-03-07 23:06:58 UTC?
**Answer:** *(Executable name with extension)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 shimcache | grep '2019-03-07 23:06:58'
```

---

### Q14: What was written in notepad.exe?
**Answer:** `flag<...>`

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 memdump -p 3032 --dump-dir=.
strings -e l 3032.dmp | grep flag
```

---

### Q15: What is the short name of the file at record 59045?
**Answer:** *(Extracted from MFT)*

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 mftparser | grep -C 15 59045
```

---

### Q16: What was the infected PID running Meterpreter?
**Answer:** 3496

**How I found it:**
```bash
vol.py -f Triage-Memory.mem --profile=Win7SP1x64 yarascan -y metasploit.yar
```

---

## Tools & Techniques Used

- **Volatility:** Memory forensics framework
- **YARA:** Malware detection (Meterpreter)
- **netscan:** Network connection analysis
- **dlllist:** DLL usage analysis
- **vadinfo:** Memory region permissions
- **hashdump:** Credential extraction

---

## Key Takeaways

- Memory analysis reveals artifacts unavailable on disk
- Meterpreter infections are identifiable via YARA
- VAD and DLL analysis highlight malicious behavior
- Credentials and user activity can be recovered from RAM

---

## MITRE ATT&CK Mapping

| Technique | ID | Evidence |
|--------|----|---------|
| Credential Access | T1003 | hashdump |
| Command-Line Interface | T1059 | cmdline |
| Process Injection | T1055 | Malicious PID |
| Remote Access Tool | T1219 | Meterpreter |
| Network Connections | T1049 | netscan |

---

