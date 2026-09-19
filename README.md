Cybersecurity Assessment Lab
A portfolio-focused cybersecurity assessment of an intentionally vulnerable Metasploitable 2 virtual machine in an isolated VMware lab. The assessment combines reconnaissance, web security testing, vulnerability management, packet analysis, and digital forensics.
> **Scope:** Authorized lab activity against `192.168.216.132` only. No public or third-party systems were tested.
Lab Environment
Component	Role	Address / Notes
Kali Linux	Assessment and analysis workstation	`192.168.216.131`
Metasploitable 2	Intentionally vulnerable target	`192.168.216.132`
VMware	Virtualization / isolated lab network	Private `192.168.216.0/24` lab
Assessment Workflow
```text
Lab setup
   -> Nmap reconnaissance
   -> HTTP enumeration / header analysis
   -> OWASP ZAP web assessment
   -> Greenbone/OpenVAS vulnerability assessment
   -> Wireshark traffic analysis
   -> Autopsy digital forensics
   -> Findings + remediation recommendations
```
1. Nmap Reconnaissance
A service/version scan was performed against the authorized target:
```bash
nmap -sV 192.168.216.132
```
The scan identified 23 open TCP services, including FTP, SSH, Telnet, DNS, HTTP, SMB, MySQL, PostgreSQL, VNC, IRC and Tomcat.
Key observations
`80/tcp` - Apache HTTP server `2.2.8` with PHP `5.2.4`
`1524/tcp` - bindshell service identified by Nmap
`8787/tcp` - Distributed Ruby-related service identified during the assessment
`8180/tcp` - Apache Tomcat `5.5`
Evidence: `evidence/nmap-service-scan.txt`
![Nmap service scan](screenshots/02-nmap-service-scan.png)
2. HTTP Enumeration and Header Analysis
Nmap HTTP scripts were used to identify page titles and response headers:
```bash
nmap -p 80,8180 --script http-title,http-headers 192.168.216.132
```
Additional header checks were performed with:
```bash
curl -I http://192.168.216.132
curl -I http://192.168.216.132:8180
```
The assessment identified software/version information exposed through HTTP headers, including Apache, PHP, and Apache-Coyote/Tomcat details.
Evidence:
`evidence/http-enumeration.txt`
`evidence/http-header-check.txt`
![HTTP enumeration](screenshots/03-http-enumeration.png)
![HTTP header analysis](screenshots/04-http-header-analysis.png)
3. OWASP ZAP Web Security Assessment
An automated ZAP assessment was performed against:
`http://192.168.216.132`
ZAP generated 25 alerts. Three representative findings were selected for deeper analysis rather than treating every alert as a confirmed vulnerability.
Finding 1 - Absence of Anti-CSRF Tokens
Risk: Medium
Confidence: Low
CWE: 352
Observation: ZAP did not identify a recognized anti-CSRF token in the reported HTML form.
Recommendation: Implement robust, validated CSRF protection for state-changing forms.
Finding 2 - Content Security Policy (CSP) Header Not Set
Risk: Medium
Confidence: High
CWE: 693
Observation: ZAP reported that the identified page did not set a `Content-Security-Policy` response header.
Recommendation: Configure the web/server stack to send an appropriate CSP policy.
Finding 3 - Cookie No HttpOnly Flag
Risk: Low
Confidence: Medium
Parameter: `PHPSESSID`
CWE: 1004
Observation: ZAP reported that the `PHPSESSID` cookie was set without the `HttpOnly` attribute.
Recommendation: Set `HttpOnly` on session cookies where client-side JavaScript access is not required.
Evidence: `evidence/zap-findings.md`
![OWASP ZAP scan results](screenshots/05-zap-scan-results.png)
![ZAP CSRF finding](screenshots/06-zap-csrf-analysis.png)
![ZAP CSP finding](screenshots/07-zap-csp-analysis.png)
![ZAP HttpOnly finding](screenshots/08-zap-httponly-analysis.png)
4. Greenbone/OpenVAS Vulnerability Assessment
A fresh Greenbone/OpenVAS assessment was created for the portfolio project:
Task: `Metasploitable 2 - Full Vulnerability Assessment`
The completed result view showed:
Severity	Results
Critical	13
High	11
Medium	40
Low	6
Log	90
Total displayed	160
Three Critical findings were investigated in detail.
Finding 1 - Distributed Ruby (dRuby/DRb) Multiple RCE Vulnerabilities
Severity: 10.0 (Critical)
QoD: 99%
Location: `8787/tcp`
OID: `1.3.6.1.4.1.25623.1.0.108010`
Key point: Greenbone detected a potentially dangerous remote command execution condition in the exposed DRb service.
Remediation direction: Restrict/disable unnecessary DRb exposure and apply appropriate security controls and access restrictions.
Finding 2 - Possible Backdoor: Ingreslock
Severity: 10.0 (Critical)
QoD: 99%
Location: `1524/tcp`
OID: `1.3.6.1.4.1.25623.1.0.103549`
Key point: Greenbone's detection test received a response indicating `uid=0(root) gid=0(root)` from the service.
Remediation: Greenbone recommends a full cleanup of an infected system.
Finding 3 - TWiki < 4.2.4 Multiple XSS / Command Execution Vulnerabilities
Severity: 10.0 (Critical)
QoD: 80%
Location: `80/tcp`
OID: `1.3.6.1.4.1.25623.1.0.800320`
CVEs reported by Greenbone: `CVE-2008-5304`, `CVE-2008-5305`
Key point: Greenbone reported unsafe input handling that could allow XSS and command/code execution in affected TWiki versions.
Remediation: Upgrade to TWiki 4.2.4 or later according to the vendor-fix recommendation reported by the scanner.
Evidence: `evidence/openvas-findings.md`
![OpenVAS assessment results](screenshots/09-openvas-assessment-results.png)
![OpenVAS task completed](screenshots/10-openvas-task-completed.png)
![OpenVAS dRuby finding](screenshots/11-openvas-critical-druby1.png)
![OpenVAS Ingreslock finding](screenshots/12-openvas-critical-ingreslock.png)
![OpenVAS TWiki finding](screenshots/13-openvas-critical-twiki.png)
5. Wireshark Traffic Analysis
Traffic was captured on Kali while communicating with the Metasploitable 2 VM.
The capture demonstrated:
ARP address resolution in the local VMware network
ICMP echo request/reply traffic
TCP three-way handshake (`SYN` -> `SYN/ACK` -> `ACK`)
HTTP request/response traffic including `GET / HTTP/1.1` and `HTTP/1.1 200 OK`
Security observation
The HTTP exchange was visible at the application layer because the test web service used unencrypted HTTP on TCP port 80. For systems carrying sensitive information, HTTPS/TLS should be used to protect application traffic in transit.
Evidence: `evidence/wireshark-notes.md`
![Wireshark overview](screenshots/15-wireshark-overview.png)
![Wireshark HTTP traffic](screenshots/16-wireshark-http.png)
![Wireshark TCP handshake](screenshots/17-wireshark-tcp-handshake.png)
![Wireshark ICMP traffic](screenshots/18-wireshark-icmp.png)
6. Autopsy Digital Forensics
A fresh Autopsy case was created for the Metasploitable 2 VMDK.
The 1.9 GB VMDK contained:
A Linux `ext` partition (sector range `63` to `481949`)
A Linux LVM partition (sector range `482013` to `16771859`)
Autopsy was used to inspect the `ext` filesystem and its metadata, including:
filesystem directory/file entries
timestamps
UID/GID ownership information
boot-related files
deleted filesystem entries
The deleted-file view identified entries including GRUB backup/temporary files and older kernel-related files. These entries were treated as forensic artifacts, not automatically as malicious activity.
Evidence: `evidence/autopsy-notes.md`
![Autopsy volume analysis](screenshots/19-autopsy-volume-analysis.png)
![Autopsy filesystem analysis](screenshots/20-autopsy-filesystem-analysis.png)
![Autopsy deleted files](screenshots/21-autopsy-deleted-files.png)
![Autopsy boot file contents](screenshots/22-autopsy-boot-file-contents.png)
Key Skills Demonstrated
Network reconnaissance with Nmap
HTTP enumeration and header analysis
OWASP ZAP web security testing
Greenbone/OpenVAS vulnerability management
Wireshark packet analysis
Linux filesystem and metadata analysis with Autopsy
Evidence collection and security reporting
Risk interpretation and remediation documentation
Evidence Files
All screenshots are stored in the `screenshots/` folder. Text-based analysis and supporting notes are stored in `evidence/`. Command references are stored in `notes/commands.txt`.
Portfolio Disclaimer
This project was performed in an isolated, intentionally vulnerable VMware laboratory environment for educational purposes. The findings and addresses documented here are lab evidence, not claims about public systems.
