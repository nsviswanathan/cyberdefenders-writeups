# Hammered – CyberDefenders Writeup

**Category:** Endpoint Forensics / Incident Response  
**Difficulty:** Medium  
**Tools Used:** grep, awk, sed, auth.log, kern.log, Apache logs, IPTables, APT logs

---

## Challenge Overview

In this lab, we investigate a compromised Linux web server from the perspective of a Security Operations Center (SOC) analyst.  
The objective is to analyze system and application logs to determine how attackers gained access, what actions they performed, and how they maintained persistence.

The investigation focuses on:
- SSH brute-force attacks
- Compromised user accounts
- Firewall rule manipulation
- Post-exploitation reconnaissance
- Web server access analysis

---

## Key Findings

### Q1: Which service did the attackers use to gain access?
**Answer:** SSH  

**How I found it:**  
Analyzed `auth.log` for authentication events. Numerous entries showed:
```
Accepted password for root from <IP> ssh2
```
A large number of `Failed password` attempts confirmed SSH brute-force activity.

---

### Q2: What is the operating system version of the targeted system?
**Answer:** Ubuntu  

**How I found it:**  
Reviewed `kern.log`, which revealed kernel build details:
```
Linux version 4.2.4-1ubuntu3
```

---

### Q3: What is the name of the compromised account?
**Answer:** root  

**How I found it:**  
```bash
grep "Failed password" auth.log | awk '{for(i=1;i<=NF;i++) if($i=="for") print $(i+1)}' | sort | uniq -c
```

---

### Q4: How many unique attackers successfully accessed the system after failures?
**Answer:** 6  

---

### Q5: Which attacker IP logged in successfully the most?
**Answer:** 219.150.161.20  

---

### Q6: How many requests were sent to the Apache server?
**Answer:** 365  

---

### Q7: How many firewall rules were added?
**Answer:** 6  

---

### Q8: Which scanning tool was downloaded?
**Answer:** nmap  

---

### Q9: When was the last login from attacker IP 219.150.161.20?
**Answer:** 04/19/2010 05:56:05 AM  

---

### Q10: What is the most dangerous database warning?
**Answer:**  
`mysql.user contains 2 root accounts without password!`

---

### Q11: Which account was created on Apr 26 at 04:43:15?
**Answer:** wind3str0y  

---

### Q12: What user-agent was used by attackers running scans via proxy?
**Answer:** pycscand/2.1  

---

## MITRE ATT&CK Mapping

| Technique | ID | Evidence |
|--------|----|---------|
| Brute Force | T1110 | SSH failed login attempts |
| Valid Accounts | T1078 | Root account compromise |
| Network Service Scanning | T1046 | Nmap installation |
| Create Account | T1136.001 | `useradd wind3str0y` |
