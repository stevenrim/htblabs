# 🔥 HTB CampFire-1 — Forensic Investigation of Kerberoasting 

## 🧨 The Problem  
A user reported suspicious files on their workstation, prompting the SOC team to initiate an investigation. The suspected threat was a **Kerberoasting attack** — a credential harvesting technique targeting Active Directory environments. The objective was to confirm whether such an attack occurred, determine when and how it happened, and identify the attacker’s tooling and behavior.

## 🛠 Tools Used

| Tool | Purpose |
|------|---------|
| **PECmd (Eric Zimmerman)** | Parsing Windows Prefetch files |
| **Timeline Explorer** | Visualizing timeline activity from parsed artifacts |
| **Event Viewer** | Reviewing Security and PowerShell logs |
| **PowerShell Operational Logs** | Investigating ScriptBlock activity (Event ID 4104) |
| **Splunk (Simulated)** | Demonstrating SIEM detection with SPL queries |

## 🔍 Method (Investigation Steps)

1. **Artifact Preparation**  
   - Unzipped the provided `.zip` containing:  
     - Domain Controller security logs  
     - PowerShell-Operational logs  
     - Windows Prefetch files  
   - Used `PECmd` to convert prefetch files into a CSV and analyzed them with `Timeline Explorer`.

2. **Domain Log Analysis for Kerberoasting Indicators**  
   - Filtered **Event ID 4769** in the Domain Controller’s security logs.  
   - Narrowed results using Kerberoasting-specific traits:  
     - RC4-encrypted tickets (`0x17`)  
     - Non-system service accounts  
     - Successful ticket requests  
   - Identified the suspicious request event matching the attack pattern.

3. **Correlating PowerShell Activity on Workstation**  
   - Analyzed **Event ID 4104** from the PowerShell-Operational logs.  
   - Observed a script execution policy bypass and enumeration commands.  
   - Verified that these events aligned with the timeline of the suspicious Kerberos activity.

4. **Tracing Attacker Tool Execution with Prefetch**  
   - Loaded parsed Prefetch CSV into `Timeline Explorer`.  
   - Filtered for first-time executable launches around the attack window.  
   - Identified a known credential extraction tool frequently used for Kerberoasting.  
   - Reviewed the file loading behavior to confirm its full path and timing.

## ✅ The Solution  
By correlating logs from the Domain Controller, workstation PowerShell activity, and Prefetch execution metadata, I confirmed a **Kerberoasting attack** occurred. The attacker enumerated Active Directory objects, bypassed script execution restrictions, and leveraged a post-exploitation tool to extract service tickets. This lab reinforces the importance of timeline correlation, endpoint visibility, and log analysis in detecting credential-based attacks.
> 🧠 *This lab demonstrates how even small log artifacts, when chained across sources, can expose a full adversary timeline. It’s a strong example of Blue Team analysis aligned with real-world frameworks.*

## 🧭 Framework Mapping

🔶 MITRE ATT&CK

| Tactic              | Technique                          | ID         | Description                                      |
|---------------------|-------------------------------------|------------|--------------------------------------------------|
| **Credential Access** | Kerberoasting                     | T1558.003  | Abuse of Kerberos service ticket requests        |
| **Discovery**        | Account Discovery (AD Enumeration) | T1087.002  | Domain user/service enumeration via PowerShell   |
| **Execution**        | PowerShell                         | T1059.001  | Script-based execution with policy bypass        |
| **Defense Evasion**  | Execution Policy Bypass            | T1059.001  | Running unsigned scripts to evade restrictions   |

🟦 NIST Cybersecurity Framework (CSF)

| Function   | Category             | Subcategory                                     |
|------------|----------------------|-------------------------------------------------|
| **Detect** | Anomalies and Events | Identify abnormal Kerberos ticket activity      |
| **Respond**| Analysis             | Analyze and validate suspected credential abuse |
| **Respond**| Mitigation           | Contain the threat through endpoint triage      |

🌐 ISO/IEC 27001:2013

| Control Area            | Control ID | Description                                  |
|--------------------------|------------|----------------------------------------------|
| **Access Control**       | A.9.2      | Control over user access and credentials     |
| **Operations Security**  | A.12.4     | Logging and monitoring of system activities  |
| **Incident Management**  | A.16.1     | Event detection and response procedures      |




## ⚠ Disclaimer
```
All activities, tools, and techniques presented in this portfolio were performed exclusively within legally authorized,
controlled environments for the purpose of cybersecurity education and ethical research. This work adheres to established
ethical hacking standards, relevant legal regulations, and responsible disclosure protocols. Any application of these
methods outside of approved environments without explicit authorization is strictly prohibited and may be unlawful.

This repository aligns with HackTheBox’s content usage policies. This write-up is based on a retired Hack The Box
room called CampFire-1. It does not contain walkthroughs, solutions, or flag disclosures. All content is intended to
demonstrate professional development and applied learning through high-level summaries and overviews.
```





