⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.

***

# Lab Write-Up: DOM XSS in innerHTML sink using source location.search

### Portfolio Information
* **Author:** Ayushma M
* **Main Repository:** [github.com/ayushmam81-ui/Web-Application-Security-Portfolio](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio)
* **Direct File Link:** [labs/dom-xss-innerhtml.md](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/dom-xss-innerhtml.md)

---

### 1. Target & Scenario
* **Platform:** PortSwigger Web Security Academy
* **Vulnerability Class:** DOM-based Cross-Site Scripting (DOM XSS)
* **Source:** `location.search`
* **Sink:** `innerHTML` (via elements using `document.getElementById`)
* **Objective:** Perform a cross-site scripting attack that triggers a client-side execution callback window.

---

### 2. Analysis & Methodology

#### Step 1: Initial Assessment & Identification of Constraints
I examined the application's search blog functionality to track data routing flow. The application utilizes JavaScript to extract the search query parameter from `location.search` and assign it directly to the `innerHTML` property of a target `div` element. 

A critical technical constraint was identified regarding the `innerHTML` sink: per standard modern browser implementations and HTML5 specifications, `innerHTML` will explicitly refuse to execute elements defined within raw `<script>` tags or inline `<svg>` blocks during runtime injection.

#### Step 2: Vector Selection and Evasion Logic
Because raw `<script>` tags are bypassed by the browser engine inside an `innerHTML` execution environment, I shifted to an alternative HTML element exploitation strategy utilizing native event-handler attributes. 

By injecting a broken HTML element wrapper that natively forces an execution fault, we can trigger an inline error event handler callback directly in the browser environment. The chosen payload structure uses an invalid image file reference to instantly force the execution path straight into the error engine:
```html
<img src=x onerror=prompt(1)>
