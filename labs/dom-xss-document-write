⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.

***

# Lab Write-Up: DOM XSS in document.write sink using source location.search

### Portfolio Information
* **Author:** Ayushma M
* **Main Repository:** [github.com/ayushmam81-ui/Web-Application-Security-Portfolio](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio)
* **Direct File Link:** [labs/dom-xss-document-write.md](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/dom-xss-document-write.md)

---

### 1. Target & Scenario
* **Platform:** PortSwigger Web Security Academy
* **Vulnerability Class:** DOM-based Cross-Site Scripting (DOM XSS)
* **Objective:** Perform a cross-site scripting attack that calls the `alert()` function.

---

### 2. Analysis & Methodology

#### Step 1: Initial Assessment & Identification of Constraints
Here 1st i typed `icode` in the search bar of the website. and looked for it in the source code of the page. And then we got to know that the source code doesn't show us what happens exactly so we clicked on inspect to see what exactly happens (like for example in the source code we see `<img>` but we can't find which image it is. So we use inspect.)

#### Step 2: Intercepting and Analyzing the HTTP Traffic
Then again we searched for `icode` in the search bar and then we clicked on inspect and in the html code we searched for `icode` and then we right clicked on the command line that showed `icode` and clicked on edit html. Sink: Input is inside an image tag's source URL, specifically the `key` parameter.

#### Step 3: Manipulation & Successful Exploitation
Then again we seared for `"><script>alert(1)</script>` this is a payload and by entering this command in the search bar a pop-up window appeared on the screen and we cleared the lab. Action: The payload closes the `key` parameter value ( `"` ), closes the `<img>` tag ( `>` ), and then injects and executes the malicious `<script>`.

---

### 3. Visual Evidence

#### Lab Objective Context:
![Lab Objective Description](../images/dom_xss_objective.png)
*Figure 1: The initial challenge parameters outlining the document.write sink and location.search source target.*

#### Error Triggered by Server Balance Check:
![Initial Balance Constraint Check](../images/dom_inspection_source.png)
*Figure 2: Inspecting the HTML elements to identify where the search query reflects within the source attribute code.*

#### Parameter Modification in Burp Repeater:
![Burp Suite Parameter Modification](../images/alert_popup_triggered.png)
*Figure 3: Modifying the parameter directly inside the search inputs to capture the alert callback trigger.*

---

### 4. Remediation Strategy
To secure this transaction sequence, the developer must stop relying on client-side inputs for static variables:
1. **Implement Server-Side Validation:** Avoid passing direct input variables into dangerous front-end sinks like `document.write()`.
2. **Discard Client-Sent Pricing Data:** Shift to secure alternative APIs like `textContent` or use context-aware output encoding to handle raw strings safely.
