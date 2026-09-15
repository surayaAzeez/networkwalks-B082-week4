![Target](https://img.shields.io/badge/Target-medirozahospital.com-red?style=for-the-badge&logo=target)
![Type](https://img.shields.io/badge/Type-Black--box%20Pentest-orange?style=for-the-badge&logo=security)
![Duration](https://img.shields.io/badge/Duration-3%20Days-blue?style=for-the-badge&logo=clock)
![Authorization](https://img.shields.io/badge/Authorization-Granted-brightgreen?style=for-the-badge&logo=check)

# Black-Box Penetration Testing Report: Mediroza General Hospital

This repository contains the full methodology, exploitation deliverables, visual proof of concepts, and remediation analysis for the Networkwalks Batch B082 (Week 4) penetration testing project targeting Mediroza General Hospital.

---

## 📑 Table of Contents
- [Executive Summary](#-executive-summary)
- [Project Overview & Rules of Engagement](#-project-overview--rules-of-engagement)
- [Key Findings & Vulnerability Matrix](#-key-findings--vulnerability-matrix)
- [Detailed Milestone Execution](#-detailed-milestone-execution)
  - [Milestone 1: Reconnaissance & Initial Access](#milestone-1-reconnaissance--initial-access)
  - [Milestone 2: Cryptanalysis & Password Cracking](#milestone-2-cryptanalysis--password-cracking)
  - [Milestone 3: Server Data Exposure & Analysis](#milestone-3-server-data-exposure--report-analysis)
  - [Milestone 4: Executive Report & Deliverables](#milestone-4-executive-report--deliverables)
- [Remediation & Action Plan](#-remediation--action-plan)
- [Legal & Ethical Disclaimer](#-legal--ethical-disclaimer)

---

## 🔒 Executive Summary
A 3-day authorized black-box penetration test was performed against `https://medirozahospital.com` to evaluate the organization's web defense posture. The engagement successfully demonstrated critical attack chains leading to unauthorized access to restricted portals, decryption of confidential patient medical records, and server-side exposure of employee salary and shareholder records.

---

## 📋 Project Overview & Rules of Engagement

| Parameter | Engagement Details |
| :--- | :--- |
| **Client Name** | Mediroza General Hospital |
| **Target URL** | `https://medirozahospital.com` |
| **Assessment Type** | Full Black-box Penetration Testing & Vulnerability Assessment |
| **Batch / Phase** | Batch B082 \| Week 4 |
| **Testing Duration** | 3 Days |
| **Written Permission** | Granted (Explicit Authorization) |

**Scope & Rules:**
* **Authorized Domain:** Testing strictly restricted to `https://medirozahospital.com`.
* **Prohibited Actions:** No Social Engineering, no Denial of Service (DoS/DDoS), no testing outside target scope.

---

## ⚠️ Key Findings & Vulnerability Matrix

| ID | Vulnerability Description | Severity | Impacted Component | CVSS v3.1 |
| :--- | :--- | :--- | :--- | :--- |
| **VULN-01** | Authentication Bypass / Broken Access Control | **CRITICAL** | Web Application Portal | 9.8 |
| **VULN-02** | Weak File Encryption Standards | **HIGH** | Patient PDF Lab Reports | 7.5 |
| **VULN-03** | Sensitive Information Disclosure in Metadata | **HIGH** | Server Files & Metadata | 7.5 |
| **VULN-04** | Unrestricted Server Data Exposure | **CRITICAL** | Internal Server Repository | 9.1 |

---

## 🛠️ Detailed Milestone Execution

### Milestone 1: Reconnaissance & Initial Access
**Objective:** Conduct reconnaissance, map web application attack surfaces, exploit input handling weaknesses, and bypass authentication to retrieve confidential patient files.

* **Reconnaissance & Portal Enumeration:** Mapped active web endpoints, identifying the Patient Portal authentication interface.

  ![Patient Portal Interface](./screenshots/Screenshot%202026-09-15%20214525.png)

* **Credential Brute-Forcing & Access Control Exploitation:** Utilized Burp Suite Intruder to perform credential testing against `https://medirozahospital.com/patient/login.php`, successfully identifying valid credentials (`admin : P@55w0rd!`) resulting in a `302 Found` redirection.

  ![Burp Suite Intruder Attack](./screenshots/Screenshot%202026-09-15%20214711.png)

* **Portal Access & File Exfiltration:** Authenticated into the patient lab report dashboard and exfiltrated 3 protected patient PDF lab reports (`S. Dlamini`, `P. Reddy`, and `E. Thompson`).

  ![Patient Lab Reports Dashboard](./screenshots/Screenshot%202026-09-15%20214720.png)

---

### Milestone 2: Cryptanalysis & Password Cracking
**Objective:** Analyze the encryption mechanisms of the 3 retrieved PDF lab reports and perform cryptanalysis to recover their full contents.

* **PDF Hash Extraction:** Parsed the encrypted PDF structures using Networkwalks crackable hash extraction tools to derive hash strings compatible with `pdf2john` and `hashcat` (Revision 3 / 128-bit key length).

  ![PDF Hash Extraction](./screenshots/Screenshot%202026-09-15%20214553.png)

* **Hash Decryption (Simple Passphrase):** Executed a dictionary attack deriving keys via MD5 + RC4, successfully cracking the password `123456` for `patient_report_1.pdf`.

  ![Password Cracked 123456](./screenshots/Screenshot%202026-09-15%20214739.png)

* **Hash Decryption (Special Character Passphrase):** Performed targeted wordlist recovery to crack complex passphrase controls, successfully uncovering `!@#$%^&` for the encrypted lab records.

  ![Password Cracked Special Characters](./screenshots/Screenshot%202026-09-15%20214818.png)

* **Recovered Patient Content:** Unlocked and verified cleartext contents of all confidential lab reports.

  | Patient Report 1 (Sipho Dlamini) | Patient Report 2 (Emily Thompson) |
  | :---: | :---: |
  | ![Lab Report Sipho Dlamini](./screenshots/Screenshot%202026-09-15%20214756.png) | ![Lab Report Emily Thompson](./screenshots/Screenshot%202026-09-15%20214539.png) |

---

### Milestone 3: Server Data Exposure & Report Analysis
**Objective:** Perform forensic analysis on extracted file properties and metadata to locate hidden server exposures containing sensitive financial and corporate records.

* **Metadata Inspection via ExifTool:** Analyzed the decrypted PDF properties using `exiftool` on Kali Linux. Discovered critical internal administrative notes stored in the `Comments` field: `DB backup moved to /old before site migration, do not delete`.

  ![ExifTool Metadata Analysis](./screenshots/Screenshot%202026-09-15%20214608.png)

* **Server Directory Exploitation & Exfiltration:** Navigated to the exposed `/old` backup directory on the server, extracting sensitive financial files containing full employee salary structures and hospital shareholder details.

---

### Milestone 4: Executive Report & Deliverables
**Objective:** Compile all findings, technical proof of concepts, CVSS risk ratings, and remediation steps into a formal penetration test report.

**Report Deliverables:**
* Complete Penetration Testing Report submitted to the client covering Executive Summary, Technical Scope, Reproduction Steps, Vulnerability Risk Ratings, and Actionable Countermeasures.

---

## 🛡️ Remediation & Action Plan
[Critical Priority] ----> Implement Server-Side Access Control & Fix Input Handling 

[High Priority]     ----> Upgrade PDF Encryption to AES-256 & Enforce Strong Passphrases

[Medium Priority]   ----> Strip File Metadata & Audit Public Server Repositories


| Vulnerability | Remediation Action |
| :--- | :--- |
| **Authentication Bypass** | Enforce strict server-side authentication checks, rate limiting, and parameterized input validation. |
| **File Encryption** | Enforce strong encryption algorithms (AES-256) and mandatory complex passphrases for sensitive patient records. |
| **Data Exposure** | Sanitize document metadata before publishing and restrict directory listing/unauthorized directory access on internal servers. |

---

## ⚖️ Legal & Ethical Disclaimer
*This repository and its contents are strictly for educational and defensive cybersecurity research purposes. All testing activities were executed under written permission within an authorized environment provided by Networkwalks for Mediroza General Hospital. Unauthorized testing against external infrastructure is strictly illegal.*


