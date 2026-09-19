# OWASP ZAP Findings Selected for Portfolio Documentation

## 1. Absence of Anti-CSRF Tokens
- Risk: Medium
- Confidence: Low
- CWE: 352
- Target: http://192.168.216.132/twiki/TWikiDocumentation.html
- Observation: ZAP did not identify a recognized anti-CSRF token in the reported HTML form.
- Recommendation: Implement robust, validated CSRF protection for state-changing forms.

## 2. Content Security Policy (CSP) Header Not Set
- Risk: Medium
- Confidence: High
- CWE: 693
- Target: http://192.168.216.132/view/TWiki/TWikiHistory?rev=1.8
- Observation: No Content-Security-Policy response header was reported for the identified page.
- Recommendation: Configure an appropriate CSP header for the application/server stack.

## 3. Cookie No HttpOnly Flag
- Risk: Low
- Confidence: Medium
- Parameter: PHPSESSID
- CWE: 1004
- Target: http://192.168.216.132/dvwa/
- Observation: The PHPSESSID cookie was set without the HttpOnly attribute.
- Recommendation: Set HttpOnly on session cookies where client-side script access is not required.
