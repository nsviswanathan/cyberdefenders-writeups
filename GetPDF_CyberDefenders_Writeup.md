# GetPDF – CyberDefenders Writeup

**Category:** Malware Analysis / Network Forensics  
**Difficulty:** Medium  
**Tools Used:** Wireshark, pdfid, pdf-parser, CyberChef, scdbg, Python

---

## Challenge Overview

In this lab, we analyze a malicious PDF delivery attack using a PCAP file.  
The attack chain involves a compromised website serving obfuscated JavaScript that silently downloads and executes a malicious PDF exploiting vulnerabilities in Adobe Acrobat Reader.

---

## Key Findings

### Q1: How many URL paths are involved in this incident?
**Answer:** 5

---

### Q2: What is the URL which contains the JS code?
**Answer:**  
http://blog.honeynet.org.my/forensic_challenge/

---

### Q3: What is the URL hidden in the JS code?
**Answer:**  
http://blog.honeynet.org.my/forensic_challenge/getpdf.php

---

### Q4: What is the MD5 hash of the PDF file?
**Answer:**  
659cf4c6baa87b082227540047538c2a

---

### Q5: How many objects are contained inside the PDF file?
**Answer:** 19

---

### Q6: How many filtering schemes are used for the object streams?
**Answer:** 4

---

### Q7: What is the object number that might contain malicious JS?
**Answer:** 5

---

### Q8: Which object streams contain the JS executing shellcode?
**Answer:** 7, 9

---

### Q9: What is the full path of the dropped malicious executable?
**Answer:**  
C:\WINDOWS\system32\a.exe

---

### Q10: What is the URL of the executable dropped by CVE-2010-0188?
**Answer:**  
http://blog.honeynet.org.my/forensic_challenge/the_real_malware.exe

---

### Q11: How many CVEs are included in the PDF file?
**Answer:** 5

---

## MITRE ATT&CK Mapping

| Technique | ID | Evidence |
|--------|----|---------|
| Drive-by Compromise | T1189 | Malicious webpage |
| User Execution | T1204.002 | Opening PDF |
| Exploitation for Client Execution | T1203 | Adobe Reader exploits |
| Obfuscated Files or Information | T1027 | Encoded JS |

---

