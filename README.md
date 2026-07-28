# Web Application Security & Vulnerability Assessment Portfolio

Hands-on vulnerability assessments targeting OWASP Top 10, CSRF, and business logic flaws within staging environments. This repository documents exploit mechanics, root cause analyses, and defensive remediation strategies conducted within dedicated lab environments to analyze structural software flaws and implement secure server-side controls.

---

## Professional Overview
I am a Forensic Science graduate specializing in digital forensics, web application security, and threat detection. This portfolio showcases my ability to apply an investigative, evidence-driven approach to map system flaws, bypass weak validation layers, and implement secure software controls across complex vulnerability vectors.

---

## 1. Access Control & Privilege Escalation

*   **Core Concepts**: 
    *   **Authentication**: Verifies user identity (e.g., username/password).
    *   **Session Management**: Identifies users across subsequent HTTP requests, often using unique identification tokens like cookies.
    *   **Access Control**: Determines whether an authorized user is permitted to perform a specific action.
*   **Types of Access Controls**:
    *   **Vertical**: Restricts access based on privilege levels; for example, only administrators should access specific features. Reliance on "security by obscurity" (e.g., hiding URLs or relying on `robots.txt`) is insufficient as these can be guessed or discovered.
    *   **Horizontal**: Prevents users of the same privilege level from accessing each other's resources.
    *   **Context-Dependent**: Restricts access based on the application state or user interaction (e.g., requiring reconfirmation before destructive actions).
*   **Broken Access Control**: Occurs when applications fail to properly restrict unauthenticated users, allowing unauthorized viewing, modification, or deletion of content.
*   **Privilege Escalation Patterns**:
    *   **Horizontal Privilege Escalation**: Attacker accesses resources of another user at the same privilege level, often by exploiting reliance on untrusted client-side inputs (e.g., brute-forcing IDs via proxy).
    *   **Vertical Privilege Escalation**: Attacker gains unauthorized access to privileged functionality by modifying parameters (e.g., changing a URL parameter to `admin=true`).
    *   **Multi-Step Process Failures**: Vulnerabilities where permissions are validated at steps 1 and 3, but skipped at step 2, allowing attackers to bypass security.
*   **Impact (CIA Triad)**: Unauthorized access affects **Confidentiality** (accessing other users' data), **Integrity** (updating other users' data), and **Availability** (deleting users).

### Practical Lab Assessments (Vertical Access Control & Bypass Labs)
*   **Lab 1: Unprotected Admin Functionality (`robots.txt`)**
    *   **Objective:** Discover hidden administrative paths exposed via search engine crawling directives (`robots.txt`) and access the admin panel to remove target accounts.
    *   **Exploitation Methodology:** Appended `/robots.txt` to the target URL to review disallowed paths, extracted the hidden administrator panel path, and navigated directly to it to complete administrative functions.
    *   📄 [View Step-by-Step Lab Write-Up](labs/unprotected-admin-functionality.md)
*   **Lab 2: Hidden URL / Security by Obscurity**
    *   **Objective:** Locate obfuscated administrator panel links embedded dynamically within client-side application code.
    *   **Exploitation Methodology:** Inspected page source markup to identify obfuscated function patterns matching internal role checks (`isAdmin`), extracted the generated path suffix following the attribute definition, and used it to access the panel and delete the user `carlos`.
    *   📄 [View Step-by-Step Lab Write-Up](labs/hidden-url-script.md)
*   **Lab 3: Admin Privilege from Client-Side Cookies**
    *   **Objective:** Exploit insecure session storage configurations where user privilege states are tracked directly via mutable cookies.
    *   **Exploitation Methodology:** Authenticated using standard user credentials (`wiener:peter`), inspected browser cookies, altered the stored privilege parameter value to indicate administrative status (`admin=true`), and accessed administrative routes.
    *   📄 [View Step-by-Step Lab Write-Up](labs/admin-privilege-cookies.md)
*   **Lab 4: User Role Modified in User Profile (`roleid`)**
    *   **Objective:** Bypass vertical access restrictions by manipulating parameter-based role identifiers during profile update requests.
    *   **Exploitation Methodology:** Captured the profile update request in Burp Suite, elevated the `roleid` parameter from standard user privileges (`1`) to administrative privileges (`2`), and submitted the modified request to unlock the admin panel and delete `carlos`.
    *   📄 [View Step-by-Step Lab Write-Up](labs/roleid-parameter-manipulation.md)
*   **Lab 5: URL-based Access Control Can Be Circumvented (`X-Original-URL`)**
    *   **Objective:** Circumvent perimeter security filters protecting an unauthenticated administrative panel by abusing framework-level URL-override headers.
    *   **Exploitation Methodology:** Leveraged the backend framework's support for the `X-Original-URL` header by sending a request to a permitted path while overriding the internal destination header to point to `/admin` and subsequently `/admin/delete?username=carlos`, bypassing front-end blocking logic.
    *   📄 [View Step-by-Step Lab Write-Up](labs/url-based-access-control-x-original-url.md)
*   **Lab 6: Method-based Access Control Can Be Circumvented**
    *   **Objective:** Exploit flawed access control logic that restricts sensitive administrative functions based solely on the HTTP request method.
    *   **Exploitation Methodology:** Intercepted restricted role-upgrade administrative functions configured to block specific methods, changed the request method from `POST` to `GET` while supplying a valid user session cookie, and successfully executed privilege elevation.
    *   📄 [View Step-by-Step Lab Write-Up](labs/method-based-access-control.md)

---

## 2. Cross-Site Request Forgery (CSRF) Analysis

* **Objective:** Audit session-handling mechanics to identify state-changing actions vulnerable to cross-site request hijacking.
* **Vulnerability Identified:** The application relied exclusively on automated, cookie-based sessions for identity validation without integrating unpredictable server-side verification factors.

### Attack Mechanics & Conditions
The attack exploits the trust a web application has in a user's browser, relying on three key conditions: a state-changing action, cookie-based session handling, and a lack of unpredictable parameters in the request. The execution involves three main actors:
1. **The Victim:** Authenticated into the vulnerable web application (`bank.com`) with an active session cookie.
2. **The Attacker:** Hosts a malicious page (`attacker.com`) containing hidden forms or auto-submitting elements.
3. **The Target Application:** Trusts incoming requests containing valid session cookies.

### Execution Steps
1. **Session Establishment:** The victim logs into the target site and receives a valid session cookie.
2. **Malicious Interaction:** The victim visits the attacker-controlled site while remaining authenticated to the target site.
3. **Request Forgery:** The malicious page automatically triggers a forged request (e.g., via a hidden form or image tag) targeting sensitive actions on the application.
4. **Implicit Cookie Inclusion:** The victim's browser automatically appends the active session cookies to the cross-site request.
5. **Unauthorized Execution:** The server processes the request as legitimate because it cannot differentiate between user-intended actions and cross-site forgery.

### Defense & Remediation Framework
* **CSRF Tokens:** Implement unique, secret, and unpredictable tokens generated by the server per user session. The server must validate this token inside hidden form fields or request headers before executing any state-changing action.
* **SameSite Cookie Attributes:** Configure session cookies with `SameSite=Lax` or `SameSite=Strict` attributes to systematically block the browser from attaching cookies during cross-site requests.
* **Referer/Origin Header Validation:** Verify the HTTP `Referer` or `Origin` headers to ensure requests originate strictly from the application's trusted domain.
* **Re-authentication:** Force users to re-enter credentials or secondary verification factors before completing critical operations like email or password modifications.

---

## 3. Business Logic & Parameter Manipulation

* **Core Concepts**:
    * **Business Logic Vulnerabilities**: Flaws in the design and implementation of an application that allow users to perform unintended behaviors, occurring not because code is broken, but because of bad assumptions about user workflows and constraints .
    * **Common Misassumptions**: Applications often mistakenly assume people will only click buttons in the correct sequence, that users will never attempt to cheat or bypass rules, and that client-side parameters are inherently trustworthy .
    * **Impact (CIA Triad / Business Risks)**: Allows attackers to obtain items for free, bypass security controls, and unlawfully access resources or other user accounts .
* **Common Attack Vectors & Patterns**:
    * **Workflow Sequencing Flaws**: Bypassing mandatory application states by executing steps out of order or skipping verification checks until final submission.
    * **Parameter & Boundary Manipulation**: Intercepting and altering numerical parameters, quantities, or financial pricing arrays within client-to-server requests.

### Practical Lab Assessments
*   **Lab 1: Excessive Trust in Client-Side Controls**
    *   **Objective:** Exploit web applications that rely on client-side price variables during financial transactions by intercepting and modifying parameters .
    *   **Exploitation Methodology:** Authenticated with provided credentials, located the specified product, captured the add-to-cart request using an interception proxy, and altered the price parameter before purchasing the item .
    *   📄 [View Step-by-Step Lab Write-Up](labs/excessive-trust-client-controls.md)  
*   **Lab 2: High-Level Logic Vulnerabilities**
    *   **Objective:** Bypass purchasing limits and balance restrictions on items by injecting boundary values or negative numbers into shopping cart parameters .
    *   **Exploitation Methodology:** Logged in with a fixed store balance, searched for the target jacket, and injected negative quantities alongside additional products to offset totals and make the item affordable .
    *   📄 [View Step-by-Step Lab Write-Up](labs/high-level-logic-vulnerabilities.md)  
*   **Lab 3: Inconsistent Security Controls**
    *   **Objective:** Access restricted administrative directories by exploiting loose validation checks and inconsistent filtering on user registration fields .
    *   **Exploitation Methodology:** Attempted to access restricted paths via `/admin`, created a test account using the integrated email client, registered an account containing the required domain substring (`dontwannacry`), and successfully accessed the admin panel to delete `carlos` .
    *   📄 [View Step-by-Step Lab Write-Up](labs/inconsistent-security-controls.md)  
*   **Lab 4: Flawed Enforcement of Business Rules**
    *   **Objective:** Exploit applications that fail to restrict the reuse or sequencing of promotional discounts and coupon codes .
    *   **Exploitation Methodology:** Added target items to the cart, applied initial promotional codes found across the user interface, and sequentially alternated multiple discount codes to infinitely stack coupons and bypass single-use promotional limits .
    *   📄 [View Step-by-Step Lab Write-Up](labs/flawed-enforcement-of-business-rules.md)  
 

---

## 4. Registration Logic & Access Control Bypass

* **Objective:** Audit role-based access control configurations and entry parameters on administrative panels (`/admin`).
* **Vulnerability Identified:** The web application utilized inconsistent domain string validation during the public profile creation workflow.
* **Exploitation Methodology:** Reverse-engineered conditional checks by embedding a specific corporate domain substring inside a basic testing email field. The weak boundary parser accepted the string, satisfying the validation logic and accidentally granting full administrative access to user-deletion panels.
* **Remediation Implemented:** Enforced a robust server-side domain verification control using strict regular expressions and structural checks, while defaulting to an explicit 'deny-all' stance for privileged paths.

---

## 5. Cross-Site Scripting (XSS) Proficiency

* **Theoretical Foundations**:
    * **Cross-Site Scripting (XSS)**: A critical web security vulnerability that enables attackers to inject malicious JavaScript into websites viewed by other users.
    * **Primary Vectors**:
        * **Reflected XSS**: The malicious script originates from the current HTTP request.
        * **Stored XSS**: The script is persisted permanently within the website's database or server and served to users upon page load.
        * **DOM-Based XSS**: The vulnerability exists entirely within client-side code structures, executing locally in the victim's browser without ever sending the payload to the server.
    * **Verification Mechanics**: While `alert()` was historically used to prove XSS, modern browser security restrictions (such as in Chrome) often require functions like `print()` or `prompt()` to bypass restrictions in specific contexts like iframes.
    * **Impact**: Exploitation allows attackers to bypass security restrictions like the same-origin policy, masquerade as victim users, steal sensitive session data, or perform unauthorized actions on behalf of the user.

### Practical Lab Assessments

#### A. DOM-Based Cross-Site Scripting (DOM XSS)
* **Objective:** Analyze client-side JavaScript execution paths to isolate instances where untrusted browser sources transfer malicious data natively into execution sinks.
* **Labs Tested:**
    * **DOM XSS in `document.write` sink using source `location.search`**: Injected script payloads via search input parameters handled unsafely by a `document.write` execution sink, using `"><script>alert(1)</script>` to close existing tags and execute code.
        * 📄 [View Step-by-Step Lab Write-Up: DOM XSS in document.write](labs/dom-xss-document-write.md)
    * **DOM XSS in `innerHTML` sink using source `location.search`**: Explored innerHTML constraints where standard script and SVG tags are restricted, utilizing alternative event handlers like `<img src=x onerror=prompt(1)>`.
        * 📄 [View Step-by-Step Lab Write-Up: DOM XSS in innerHTML](labs/dom-xss-innerhtml.md)
    * **DOM XSS in jQuery anchor `href` attribute sink using `location.search` source**: Manipulated jQuery code taking data from URLs and assigning it dynamically to anchor elements, using `javascript:` pseudo-protocol payloads.
        * 📄 [View Step-by-Step Lab Write-Up: DOM XSS in jQuery href Attribute](labs/dom-xss-jquery-anchor.md)
    * **DOM XSS in jQuery selector sink using a hashchange event**: Targeted older versions of jQuery where passing HTML elements or payloads to selector functions constructs elements and triggers unintended execution via hash changes.
        * 📄 [View Step-by-Step Lab Write-Up: DOM XSS in jQuery Selector](labs/dom-xss-jquery-selector.md)

#### B. Stored/Persistent Cross-Site Scripting (Stored XSS)
* **Objective:** Audit application functions that permanently save user-supplied data to the server database to identify vectors where malicious scripts are served to subsequent visitors without adequate validation.
* **Labs Tested:**
    * **Stored XSS into HTML context with nothing encoded**: Checked comment and name input fields, finding that angle brackets were unencoded in comment parameters, allowing the injection of scripts like `<src/onload=alert(123)//>`.
        * 📄 [View Step-by-Step Lab Write-Up: Stored XSS into HTML Context](labs/stored-xss-html-context.md)
    * **Stored XSS into anchor href attribute with double quotes HTML-encoded**: Identified vulnerable website input fields in comment sections where angle brackets were encoded except within specific URI fields, allowing execution via `javascript:alert(123)`.
        * 📄 [View Step-by-Step Lab Write-Up: Stored XSS into Anchor href Attribute](labs/stored-xss-anchor-href.md)

#### C. Reflected Cross-Site Scripting (Reflected XSS)
* **Objective:** Investigate how malicious scripts are injected into HTTP requests and reflected back by the application, specifically focusing on bypassing Web Application Firewalls (WAFs) and restrictive tag-filtering policies.
* **Labs Tested:**
    * **Basic Reflected XSS**: Examined unencoded reflection behavior using search parameters and injected script tags containing custom vectors.
        * 📄 [View Lab: Basic Reflected XSS](labs/reflected-xss-html-context.md)
    * **Reflected XSS into attribute with angle brackets HTML-encoded**: Evaluated attribute context reflection and bypassed quote filters using event-handler injections like `avatar" autofocus onfocus="alert(123)`.
        * 📄 [View Lab: Reflected XSS into Attribute](labs/reflected-xss-attribute-context.md)
    * **Reflected XSS into a JavaScript string with angle brackets HTML encoded**: Bypassed single-quote constraints inside JavaScript strings to inject code segments cleanly.
        * 📄 [View Lab: XSS into JavaScript Strings](labs/reflected-xss-js-string.md)
    * **Reflected XSS with some SVG markup allowed**: Leveraged allowed SVG elements and animation triggers like `<svg><animatetransform onbegin="alert(123)"></animatetransform></svg>`.
        * 📄 [View Lab: Exploiting Allowed SVG Markup](labs/reflected-xss-svg-markup.md)
    * **Reflected XSS into HTML context with most tags and attributes blocked**: Utilized Burp Suite Intruder to discover unblocked custom tags and attributes (such as `onbeforeinput`) behind a strict WAF.
        * 📄 [View Lab: WAF Bypass and Tag Filtering](labs/reflected-xss-waf-bypass.md)
    * **Reflected XSS into HTML context with all tags blocked except custom ones**: Deployed custom tags wrapped inside `iframe` elements to execute payload strings securely.
        * 📄 [View Lab: Custom Tag Injection](labs/reflected-xss-custom-tags.md)
---

## 6. Automated Injection Vectors (SQLi & Base XSS)

* **Objective:** Detect and validate server-side databases and basic application data flow boundaries using automated security frameworks.
* **SQL Injection (SQLi) Proficiency:**
    * **Automated Enumeration:** Utilized `sqlmap` to perform database fingerprinting, identify current users/hostnames, and enumerate database schemas.
    * **Exploitation & Data Extraction:** Mastered flags for targeted data extraction (`-D <db> -T <table> --dump`), handling authentication (`--cookie`), and processing complex requests captured via proxy (`-r file.req`).
    * **Advanced Evasion:** Applied tamper scripts (e.g., `space2comment`) to bypass Web Application Firewalls (WAF) and utilized context-aware techniques to minimize noise and avoid triggering rate limits.
    * **Risk Management:** Strictly adhered to ethical testing boundaries by maintaining low `--risk` levels (1-2) during production-style assessments to prevent unintended data modification.
* **Basic XSS Testing Context:** Audited reflection behaviors to evaluate core application field filtering constraints across standard inputs.
* **Remediation Implemented:** Deployed strictly parameterized structural database operations (prepared statements) to neutralize injection vectors, and mandated explicit encoding rules covering all external web browser output layers.

---

## 7. Directory Traversal Filter Evasion

* **Theoretical Foundations**:
    * **Path Traversal (Directory Traversal)**: A web security vulnerability that allows attackers to read arbitrary files on the server running an application, such as application code, backend system credentials, and sensitive operating system files (e.g., `/etc/passwd` on Unix-like systems or `win.ini` on Windows).
    * **Core Navigation**: Uses relative traversal sequences like `../` on Unix or `..\` on Windows to move backward out of specific folders and access unauthorized directories.
    * **Impact**: Unauthorized read access to sensitive system files, configuration data, and potentially file modification if permissions allow.

* **Common Obstacles & Bypass Techniques**:
    * **Absolute Path Bypass**: When basic traversal sequences like `../` are blocked, attackers can directly reference files using absolute paths from the filesystem root (e.g., `filename=/etc/passwd`).
    * **Nested Traversal Sequences**: Utilizing patterns like `....//` which bypass naive filters that strip inner traversal sequences, leaving behind valid paths.
    * **URL Encoding & Obfuscation**: Encoding characters to bypass server-side cleaning filters (e.g., `%2e%2e%2f` for `../` or double URL encoding like `%252e%252e%252f`).

### Practical Lab Assessments

* **Objective:** Navigate out of restricted application file structures to safely test input verification mechanics.
* **Vulnerability Identified:** Web application endpoints processed file paths without adequate canonicalization or path sanitization.
* **Exploitation Methodology:** Implemented filter evasion techniques using absolute path references, nested traversal patterns, and double-URL encoding to bypass perimeter sanitization code.
* **Lab Write-Ups:**
    * 📄 [View Step-by-Step Lab Write-Up: Reading arbitrary files via path traversal](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/path-traversal-lab1.md)
    * 📄 [View Step-by-Step Lab Write-Up: Path Traversal with Absolute Path Bypass](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/path-traversal-lab2.md)
    * 📄 [View Step-by-Step Lab Write-Up: Path Traversal with Nested Traversal Sequence Bypass](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/path-traversal-lab3.md)
    * 📄 [View Step-by-Step Lab Write-Up: Path Traversal with URL Encoding Bypass](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/path-traversal-lab4.md)
* **Remediation Implemented:** Avoided passing direct input variables into file-system APIs, shifting instead to a hard whitelisting framework for file extensions and directory paths.
---

## 8. Insecure Direct Object References (IDOR)

* **Theoretical Foundations**:
    * **Insecure Direct Object References (IDOR)**: A type of access control vulnerability that arises when an application uses user-supplied input to directly access internal database objects or static files without proper validation.
    * **Privilege Escalation Vectors**: Most commonly associated with horizontal privilege escalation (accessing other users' data of the same privilege level), but can also arise in relation to vertical privilege escalation (accessing administrative or privileged functions).
    * **Exploitation Technique**: Often performed simply by editing or manipulating parameters within the URL (e.g., changing identifiers or predictable static file names).

### Practical Lab Assessments

* **Objective:** Audit object reference mechanisms to ensure robust authorization checks are enforced whenever users attempt to access database entries or static files.
* **Vulnerability Identified:** Lack of server-side validation and authorization enforcement on direct resource identifiers and predictable file names.
* **Exploitation Methodology:** Modified URL parameters and sequential file identifiers to bypass access controls and retrieve unauthorized records and chat transcripts.
* **Remediation Implemented:** Replaced direct object references with indirect, session-mapped reference maps or enforced rigorous server-side authorization checks verifying that the currently authenticated user owns or has permission to access the requested object.
---
*Note: All assessments and testing procedures contained in this repository were executed safely within isolated, educational lab environments to research protocol vulnerabilities and structural software defense.*
