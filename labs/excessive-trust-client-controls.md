---
⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.
---

# Lab Write-Up: Excessive Trust in Client-Side Controls

### Portfolio Information
* **Author:** Ayushma M
* **Main Repository:** [github.com/ayushmam81-ui/Web-Application-Security-Portfolio](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio)
* **Direct File Link:** [labs/excessive-trust-client-controls.md](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/excessive-trust-client-controls.md)

---

### 1. Target & Scenario
* **Platform:** PortSwigger Web Security Academy
* **Vulnerability Class:** Business Logic Vulnerability / Parameter Tampering
* **Objective:** Exploit a flaw in the application's purchasing workflow to buy a "Lightweight l33t leather jacket" for an unintended, lower price.

### 2. Analysis & Methodology

#### Step 1: Initial Assessment & Identification of Constraints
I authenticated into the e-commerce store using the provided credentials. The target item, a "Lightweight l33t leather jacket", was priced at $1337.00. Attempting to purchase or add the item to the cart normally triggered an error indicating **"Not enough store credit for this purchase"**, confirming the application enforces a strict financial check based on user balance.

#### Step 2: Intercepting and Analyzing the HTTP Traffic
To inspect how the pricing structure was processed, I initiated a request to add the item to the cart and intercepted the transmission using **Burp Suite Community Edition**. I sent the resulting payload to **Burp Repeater** for detailed analysis. 

The application passed critical parameters directly through the message body of a `POST` request to `/cart`:
```http
productId=1&redir=PRODUCT&quantity=1&price=133700
