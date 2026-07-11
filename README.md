# Web Application Security & Vulnerability Assessment Portfolio

Hands-on vulnerability assessments targeting OWASP Top 10, CSRF, and business logic flaws within staging environments. This repository documents exploit mechanics, root cause analyses, and defensive remediation strategies conducted within dedicated lab environments to analyze structural software flaws and implement secure server-side controls.

---

## Professional Overview
I am a Forensic Science graduate specializing in digital forensics, web application security, and threat detection. This portfolio showcases my ability to apply an investigative, evidence-driven approach to map system flaws, bypass weak validation layers, and implement secure software controls across complex vulnerability vectors.

---

## 1. Access Control & Privilege Escalation Fundamentals

* **Core Definitions:**
    * **Authentication:** Verifies user identity via credentials[cite: 3].
    * **Session Management:** Maintains identity across HTTP requests via unique tokens[cite: 3].
    * **Access Control:** Determines if a user is permitted to perform a specific action[cite: 3].
* **Vulnerability Patterns:**
    * **Vertical Privilege Escalation:** Attacker gains unauthorized access to privileged functionality (e.g., admin panels)[cite: 3].
    * **Horizontal Privilege Escalation:** Attacker gains access to resources belonging to another user at the same privilege level[cite: 3].
    * **Context-Dependent Failures:** Vulnerabilities in multi-step processes where permissions are not verified at every individual stage[cite: 3].
* **Remediation:** Implement strict server-side authorization checks at every step, abandon "security by obscurity," and ensure access decisions are never based on client-controlled parameters[cite: 3].

---

## 2. Cross-Site Request Forgery (CSRF) Analysis

* **Objective:** Audit session-handling mechanics to identify state-changing actions vulnerable to cross-site request hijacking.
* **Defense & Remediation Framework:**
    * **CSRF Tokens:** Implement unique, secret, and unpredictable tokens validated by the server[cite: 3].
    * **SameSite Cookie Attributes:** Configure cookies with `SameSite=Lax` or `Strict` to block cross-site attachment[cite: 3].
    * **Header Validation:** Verify HTTP `Referer` or `Origin` headers[cite: 3].
    * **Re-authentication:** Force re-authentication for sensitive operations[cite: 3].

---

## 3. Business Logic & Parameter Manipulation

* **Objective:** Evaluate multi-step e-commerce workflows to ensure financial and logical constraints are strictly enforced at the server layer.
* **Vulnerabilities Assessed:**
    * **Price Manipulation:** Testing interception of API POST requests to alter client-side price parameters.
    * **Boundary Value Injection:** Using negative integers in quantity fields to offset total order values.
    * **Inconsistent Security Controls:** Exploiting loose parameter validation to escalate privileges.
    * **Workflow Rule Stacking:** Bypassing promotional limits by manipulating discount code sequences.
* **Remediation:** All transaction parameters and logic checks are calculated and validated exclusively on the server side using structural constraint matrices.

---

## 4. Cross-Site Scripting (XSS) Proficiency

* **Theoretical Foundations:** XSS enables the injection of malicious scripts into web pages viewed by other users[cite: 1].
    * **Primary Vectors:** Reflected, Stored, and DOM-based XSS[cite: 1].
    * **Impact:** Bypass of same-origin policies, session data theft, and unauthorized user actions[cite: 1].
* **Practical Lab Assessments:**
    * **DOM-Based XSS:** Analysis of client-side execution paths including `document.write`, `innerHTML`, and jQuery selectors.
    * **Stored XSS:** Auditing database-persistent input vectors in HTML and attribute contexts.
    * **Reflected XSS:** Testing WAF bypasses, custom tag injection, and SVG markup filtering.
* **Remediation:** Enforce context-aware output encoding and strict Content Security Policies (CSP).

---

## 5. Automated Injection Vectors (SQLi)

* **Objective:** Detect and validate server-side databases using automated security frameworks.
* **SQL Injection (SQLi) Proficiency:**
    * **Automated Enumeration:** Fingerprinting databases, identifying current users, and enumerating schemas using `sqlmap`[cite: 2].
    * **Advanced Evasion:** Utilizing tamper scripts (e.g., `space2comment`) to bypass Web Application Firewalls[cite: 2].
    * **Risk Management:** Maintaining low `--risk` levels during production-style assessments to prevent unintended data modification[cite: 2].
* **Remediation:** Deploying parameterized prepared statements to neutralize injection vectors[cite: 2].

---

## 6. Directory Traversal Filter Evasion

* **Objective:** Navigate out of restricted application file structures to test input verification.
* **Exploitation:** Implemented traversal patterns (`....//`) and double-URL encoding to bypass perimeter sanitization.
* **Remediation:** Shifted to a hard whitelisting framework for file extensions and directory paths, avoiding direct input in file-system APIs.

---

*Note: All assessments and testing procedures contained in this repository were executed safely within isolated, educational lab environments to research protocol vulnerabilities and structural software defense.*
