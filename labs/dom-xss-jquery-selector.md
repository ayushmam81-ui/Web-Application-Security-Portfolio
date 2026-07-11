⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.

***

# Lab Write-Up: DOM XSS in jQuery selector sink using a hashchange event

### Portfolio Information
* **Author:** Ayushma M
* **Main Repository:** [github.com/ayushmam81-ui/Web-Application-Security-Portfolio](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio)
* **Direct File Link:** [labs/dom-xss-jquery-selector.md](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/dom-xss-jquery-selector.md)

---

### 1. Target & Scenario
* **Platform:** PortSwigger Web Security Academy
* **Vulnerability Class:** DOM-based Cross-Site Scripting (DOM XSS)
* **Objective:** Perform a cross-site scripting attack using a jQuery selector sink and a hashchange event.

---

### 2. Analysis & Methodology

#### Step 1: Initial Assessment & Identification of Constraints
In older versions of jQuery, passing HTML elements to the selector function causes the library to create those elements on the page. If an XSS payload is passed to the jQuery selector, it will execute that payload.

#### Step 2: Payload Injection Strategy
In this lab, user input from the search bar is reflected in the jQuery selector and is visible in the source code. The application uses a hashchange event, meaning that whenever the hash fragment in the URL is changed, the function beside it is called and executed.

#### Step 3: Manipulation & Successful Exploitation
By inserting a payload like `<img src=x onerror=prompt(1)>` into the search bar, the jQuery selector `$(<img src=x onerror=prompt(1)>)` attempts to create an image tag. Because the source `x` is invalid, it triggers an error, which ultimately causes the `prompt(1)` function to execute.

---

### 3. Visual Evidence

#### Lab Objective Context:
![Lab Objective Description](../images/dom_jquery_selector_objective.png)
*Figure 1: Lab documentation detailing how jQuery selector functions can be abused to create malicious elements via hashchange events.*

---

### 4. Remediation Strategy
To secure this application against DOM-based XSS:
1. **Update jQuery:** Ensure the application uses the latest stable version of jQuery, which mitigates these selector-based vulnerabilities.
2. **Avoid Dynamic Selectors:** Do not pass unsanitized, user-controllable input (such as hash fragments or URL parameters) directly into jQuery selector functions.
