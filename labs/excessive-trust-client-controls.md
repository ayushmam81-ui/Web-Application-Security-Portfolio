⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.

***

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

---

### 2. Analysis & Methodology

#### Step 1: Initial Assessment & Identification of Constraints
I authenticated into the e-commerce store using the provided credentials. The target item, a "Lightweight l33t leather jacket", was priced at $1337.00. Attempting to purchase or add the item to the cart normally triggered an error indicating **"Not enough store credit for this purchase"**, confirming the application enforces a strict financial check based on user balance.

#### Step 2: Intercepting and Analyzing the HTTP Traffic
To inspect how the pricing structure was processed, I initiated a request to add the item to the cart and intercepted the transmission using **Burp Suite Community Edition**. I sent the resulting payload to **Burp Repeater** for detailed analysis. 

The application passed critical parameters directly through the message body of a POST request to /cart:

`productId=1&redir=PRODUCT&quantity=1&price=133700`

This revealed a fundamental design flaw: the application relies entirely on client-side controls to declare the value of an item, rather than managing the cost safely on the backend server.

#### Step 3: Manipulation & Successful Exploitation
Using Burp Repeater, I tampered with the parameters. I modified the `price` parameter value from `133700` directly down to `10` (manipulating the transaction price down to $0.10). 

Upon clicking **Send**, the server processed the parameter without validating it against a secure master database. The application responded with an HTTP 302 Found status, redirecting back to the product catalog and successfully placing the high-value item into the cart for pennies, solving the lab.

---

### 3. Visual Evidence

#### Error Triggered by Server Balance Check:
![Initial Balance Constraint Check](../images/insufficient_credit.png)
*Figure 1: The application blocking the purchase due to a high default price.*

#### Parameter Modification in Burp Repeater:
![Burp Suite Parameter Modification](../images/burp_manipulation.png)
*Figure 2: Modifying the client-side price parameter down to 10 in Burp Suite.*

---

### 4. Remediation Strategy
To secure this transaction sequence, the developer must stop relying on client-side inputs for static variables:
1. **Implement Server-Side Validation:** Product pricing must be pulled directly from a secure, trusted relational database on the server layer using only the productId as a reference.
2. **Discard Client-Sent Pricing Data:** The backend application should explicitly ignore or reject any price parameters transmitted within HTTP request bodies from users.
