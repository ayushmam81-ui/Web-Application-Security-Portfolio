⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.

***

# Lab Write-Up: Inconsistent Security Controls

### Portfolio Information
* **Author:** Ayushma M
* **Main Repository:** [github.com/ayushmam81-ui/Web-Application-Security-Portfolio](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio)
* **Direct File Link:** [labs/inconsistent-security-controls.md](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/inconsistent-security-controls.md)

---

### 1. Target & Scenario
* **Platform:** PortSwigger Web Security Academy
* **Vulnerability Class:** Business Logic Vulnerability / Flawed Access Controls
* **Objective:** Exploit flaws in account registration parameters to escalate privileges, access the restricted administrative routing interface, and delete user `carlos`.

---

### 2. Analysis & Methodology

#### Step 1: Initial Mapping & Access Barriers
I navigated to the application target and attempted to access the administrative directory directly by appending `/admin` to the URL path. The backend intercepted the request and dropped it, rendering an explicit message: **"Admin interface only available if logged in as a DontWannaCry user"**. This confirmed that explicit routing filters are present but rely heavily on an implicit, queryable attribute mapped to the active session profile.

#### Step 2: Extracting Registration Rules via Email Client
The application environment provided an independent email client sandbox to mimic a legitimate webmail delivery loop. I opened the email platform to provision a temporary interaction context, noting down the randomized baseline domain structure generated for external account sign-ups. 

#### Step 3: Exploiting Loose Validation (Inconsistent Substring Matching)
I attempted to bypass the system's corporate identity validation by creating a custom account registration profile. Rather than using standard formatting, I purposefully structured an email string that included the required corporate identity keyword as a nested substring identifier:

`test@dontwannacry.com`

Instead of rejecting this foreign email parameter string or restricting domain mapping to strict domain matches (e.g., matching the exact domain suffix value precisely), the registration endpoint parsed the payload loosely. By recognizing the string `dontwannacry` anywhere inside the field value, the business logic erroneously flagged the account registration as belonging to an internal employee domain.

#### Step 4: Access Escalation & Resolution
I validated the registration link through the provided sandbox email client inbox. Upon authenticating with the newly created account, the backend incorrectly initialized the session profile with full administrative permissions. 

The `/admin` path now opened a complete administrative dashboard. From this console, I initiated a user deletion command against the target profile `carlos`, successfully completing the lab requirements.

---

### 3. Visual Evidence

#### Lab Objective Context:
![Lab Parameters Summary](../images/image%20(3).png)
*Figure 1: The administrative target requirement to remove carlos.*

#### Boundary Error on Direct Access:
![Admin Access Restriction Screen](../images/image%20(4).png)
*Figure 2: Server enforcing domain restrictions on public access routes.*

#### Verification via Email Sandbox Client:
![Email Delivery Simulation Context](../images/image%20(5).png)
*Figure 3: Registering the account and validating using the email simulation client.*

#### Parameter Registration Profile Update:
![Successful Administrative Profile Assignment](../images/image%20(6).png)
*Figure 4: Bypassing rules by updating the account email to a spoofed administrative substring.*

---

### 4. Remediation Strategy
1. **Enforce Exact Domain Matching:** Rather than validating email strings via unsafe substring filters or regular expressions that look for keywords anywhere within the field, use strict, explicit whitelisting to match the exact suffix domain (e.g., verifying that the email ends precisely with `@dontwannacry.com`).
2. **Centralized Access Matrix:** Do not determine user privilege assignment solely based on account creation parameters. Decouple user attribute ingestion from access token calculations and enforce uniform role checks via a server-side authorization filter.
