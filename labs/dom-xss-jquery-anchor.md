⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.

***

# Lab Write-Up: DOM XSS in jQuery anchor href attribute sink using location.search

### Portfolio Information
* **Author:** Ayushma M
* **Main Repository:** [github.com/ayushmam81-ui/Web-Application-Security-Portfolio](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio)
* **Direct File Link:** [labs/dom-xss-jquery-anchor.md](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/dom-xss-jquery-anchor.md)

---

### 1. Target & Scenario
* **Platform:** PortSwigger Web Security Academy
* **Vulnerability Class:** DOM-based Cross-Site Scripting (DOM XSS)
* **Objective:** Perform a cross-site scripting attack that makes the "back" link alert `document.cookie`.

---

### 2. Analysis & Methodology

#### Step 1: Initial Assessment & Identification of Constraints
The lab uses the jQuery library's `$` selector function to find an anchor element and dynamically changes its `href` attribute using data from `location.search`. Through browser inspection, I identified that the "Back" link on the submit feedback page was the target element controlled by the URL parameter.

#### Step 2: Intercepting and Analyzing the HTTP Traffic
I tested the URL parameter `returnPath` by injecting the string `icode`. By observing the DOM source, I confirmed that the value provided after the `=` sign is directly reflected into the `href` attribute of the "Back" link.

#### Step 3: Manipulation & Successful Exploitation
To achieve the objective, I replaced the default path in the URL with the JavaScript URI scheme: `javascript:alert(document.cookie)`. After updating the URL, I verified the injection by inspecting the element code, which confirmed the payload was correctly placed. Finally, I clicked the "Back" link, which executed the injected code and triggered the required cookie alert pop-up.

---

### 3. Visual Evidence

#### Lab Objective Context:
![Lab Objective Description](../images/dom_jquery_objective.png)
*Figure 1: Lab requirements specifying the use of jQuery to manipulate anchor attributes via location.search.*

#### Identification of the Vulnerable Link:
![Back Link Inspection](../images/back_link_identification.png)
*Figure 2: Using the browser inspector to identify the "back" link element tied to the URL parameter.*

#### URL Parameter Manipulation:
![Parameter Modification](../images/url_parameter_manipulation.png)
*Figure 3: Injecting the test string "icode" into the returnPath parameter to confirm DOM reflection.*

#### JavaScript Payload Injection:
![Payload Injection](../images/js_alert_payload_injection.png)
*Figure 4: Replacing the parameter with the target payload: `javascript:alert(document.cookie)`.*

#### Successful Execution:
![Execution Confirmation](../images/cookie_alert_execution.png)
*Figure 5: The browser triggering the alert pop-up upon clicking the manipulated "Back" link.*

---

### 4. Remediation Strategy
To secure this application against DOM-based XSS:
1. **Avoid Dangerous Sinks:** Do not use user-controlled data to populate attributes that support execution contexts, such as `href` or `src`.
2. **Implement Strict Whitelisting:** If dynamic link generation is necessary, validate the `returnPath` against a strict whitelist of allowed URL paths on the server side before rendering.
