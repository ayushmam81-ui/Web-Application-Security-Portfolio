# Web-Application-Security-Portfolio
Hands-on vulnerability assessments targeting OWASP Top 10, CSRF, and business logic flaws within staging environments.
# Web Application Security & Vulnerability Assessment Portfolio

This repository documents hands-on security assessments, exploit mechanics, and defensive remediation strategies conducted within dedicated staging environments. The focus is on analyzing structural software flaws, bypassing weak validation layers, and implementing secure server-side controls.

---

## 1. Cross-Site Request Forgery (CSRF) Analysis
* **Objective:** Audit session-handling mechanics to identify state-changing actions vulnerable to cross-site request hijacking.
* **Vulnerability Identified:** The application relied exclusively on automated, cookie-based sessions for identity validation without integrating unpredictable server-side verification factors.
* **Exploitation Methodology:** Simulated an attacker-controlled site hosting an auto-submitting HTML form targeting the application's sensitive profile endpoints. When an authenticated user visited the malicious page, the browser implicitly appended active session cookies, forcing unauthorized execution.
* **Remediation Implemented:** Designed secure remediation guidelines requiring unique, unpredictable anti-CSRF tokens for all state-changing operations and configuring browser cookie parameters to `SameSite=Lax` or `Strict`.

---

## 2. Business Logic & Parameter Manipulation

* **Objective:** Evaluate multi-step e-commerce workflows and registration mechanics to ensure financial constraints, access states, and business rules are strictly enforced at the server layer.
* **Vulnerabilities Tested:**
    * **Price Manipulation:** Intercepted API POST requests using an interception proxy (Burp Suite) during add-to-cart operations and altered the client-side price parameter.
        * 📄 [View Step-by-Step Lab Write-Up: Excessive Trust in Client-Side Controls](labs/excessive-trust-client-controls.md)
    * **Boundary Value Injection (Negative Quantities):** Injected negative integers into shopping cart quantity fields to artificially subtract balances and offset the total order value.
        * 📄 [View Step-by-Step Lab Write-Up: High-Level Logic Vulnerabilities](labs/high-level-logic-vulnerabilities.md)
    * **Inconsistent Security Controls:** Exploited loose registration parameter validation filters via substring domain matching to escalate privileges and access restricted administrative directories.
        * 📄 [View Step-by-Step Lab Write-Up: Inconsistent Security Controls](labs/inconsistent-security-controls.md)
    * **Workflow Rule Stacking:** Bypassed single-use promotional limits by alternating distinct discount code parameters sequentially to infinitely stack coupons.
        * 📄 [View Step-by-Step Lab Write-Up: Flawed Enforcement of Business Rules](labs/flawed-enforcement-of-business-rules.md)
* **Root Cause Analysis:** The core flaws stemmed from excessive developer trust in client-side data, flawed state parsing, and a total lack of server-side validation regarding numerical boundaries, role mapping, and logical sequence progression.
* **Remediation Implemented:** Established that all transaction parameters, registration attributes, balances, and logic checks must be hard-validated and calculated exclusively on the server side using strict whitelists and structural constraint matrices.---

## 3. Registration Logic & Access Control Bypass
* **Objective:** Audit role-based access control configurations and entry parameters on administrative panels (`/admin`).
* **Vulnerability Identified:** The web application utilized inconsistent domain string validation during the public profile creation workflow.
* **Exploitation Methodology:** Reverse-engineered conditional checks by embedding a specific corporate domain substring inside a basic testing email field. The weak boundary parser accepted the string, satisfying the validation logic and accidentally granting full administrative access to user-deletion panels.
* **Remediation Implemented:** Enforced a robust server-side domain verification control using strict regular expressions and structural checks, while defaulting to an explicit 'deny-all' stance for privileged paths.

---

## 4. Core Injection Vectors & Automation (SQLi & XSS)
* **Objective:** Detect and validate server-side and client-side injection surfaces using automated testing frameworks and flow analysis.
* **Vulnerabilities Tested:**
  * **Automated SQL Injection:** Utilized raw HTTP requests inside SQLMap to map backend database schemas, tuning level/risk settings and implementing tamper scripts to bypass firewall filtering.
  * **DOM-Based XSS:** Traced untrusted user data inputs directly flowing into vulnerable JavaScript execution sinks such as `innerHTML` and `document.write`.
* **Remediation Implemented:** Enforced context-aware output encoding across all web rendering layers and deployed strictly parameterized queries (prepared statements) for database operations.

---

## 5. Directory Traversal Filter Evasion
* **Objective:** Navigate out of restricted application file structures to safely test input verification mechanics.
* **Vulnerability Identified:** Web application endpoints processed file paths without adequate canonicalization or path sanitization.
* **Exploitation Methodology:** Implemented filter evasion techniques using absolute path references, nested traversal patterns (`....//`), and double-URL encoding to bypass perimeter sanitization code.
* **Remediation Implemented:** Avoided passing direct input variables into file-system APIs, shifting instead to a hard whitelisting framework for file extensions and directory paths.

---
*Note: All assessments and testing procedures contained in this repository were executed safely within isolated, educational lab environments to research protocol vulnerabilities and structural software defense.*
