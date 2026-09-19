# Greenbone/OpenVAS Portfolio Findings

Task: Metasploitable 2 - Full Vulnerability Assessment

## Results breakdown shown in the completed Results view
- Critical: 13
- High: 11
- Medium: 40
- Low: 6
- Log: 90
- Total categorized results shown: 160

## Critical finding 1
Distributed Ruby (dRuby/DRb) Multiple RCE Vulnerabilities
- Severity: 10.0 Critical
- QoD: 99%
- Host: 192.168.216.132
- Location: 8787/tcp
- OID: 1.3.6.1.4.1.25623.1.0.108010
- Remediation direction: Restrict/disable unnecessary DRb exposure and apply appropriate access/security controls.

## Critical finding 2
Possible Backdoor: Ingreslock
- Severity: 10.0 Critical
- QoD: 99%
- Host: 192.168.216.132
- Location: 1524/tcp
- OID: 1.3.6.1.4.1.25623.1.0.103549
- Detection result reported by Greenbone: uid=0(root) gid=0(root)
- Greenbone solution: A whole cleanup of the infected system is recommended.

## Critical finding 3
TWiki < 4.2.4 Multiple XSS / Command Execution Vulnerabilities
- Severity: 10.0 Critical
- QoD: 80%
- Host: 192.168.216.132
- Location: 80/tcp
- OID: 1.3.6.1.4.1.25623.1.0.800320
- Fixed version reported: 4.2.4
- CVEs reported: CVE-2008-5304, CVE-2008-5305
- Remediation: Update to version 4.2.4 or later.
