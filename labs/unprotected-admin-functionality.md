⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.

***

# Lab Write-Up: Unprotected Admin Functionality

### Portfolio Information
* **Author:** Ayushma M
* **Main Repository:** [github.com/ayushmam81-ui/Web-Application-Security-Portfolio](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio)
* **Direct File Link:** [labs/unprotected-admin-functionality.md](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/unprotected-admin-functionality.md)

---

### 1. Target & Scenario
* **Platform:** PortSwigger Web Security Academy[cite: 6]
* **Vulnerability Class:** Vertical Access Control / Unprotected Functionality[cite: 6]
* **Objective:** Solve the lab by deleting the user `carlos`[cite: 6].

---

### 2. Analysis & Methodology

#### Step 1: Reconnaissance & Discovery
* Type `/robots.txt` on the URL to know the admin's URL to access admin privileges[cite: 6].
* Discovered an unintended exposure of the administrative path (`/administrator-panel`) within the `robots.txt` file, highlighting a classic security by obscurity flaw[cite: 6].

#### Step 2: Exploitation & Access Control Bypass
* Bypassed perimeter restrictions by navigating directly to the discovered administrative endpoint (`/administrator-panel`)[cite: 6].
* The application loaded the admin dashboard directly without validating backend authorization checks or credentials[cite: 6].

#### Step 3: Execution
* This step shows us that we’ve gained the admin privileges by typing the URL found in the file `/robots.txt`. Located target user `carlos` within the interface and successfully deleted the user to complete the lab objective[cite: 6].

---

### 3. Visual Evidence

#### Lab Execution and Output:
![Unprotected Admin Robots](../images/unprotected-admin-robots.png)
*Figure 1: Type /robots.txt on the URL to know the admins URL to access admin privileges[cite: 6].*

![Unprotected Admin Dashboard](../images/unprotected-admin-dashboard.png)
*Figure 2: This step shows us that we’ve gained the admin privileges by typing the URL found in the file /robots.txt[cite: 6].*

![Lab Solved](../images/unprotected-admin-solved.png)
*Figure 3: Lab successfully solved by deleting user carlos[cite: 6].*

---

### 4. Remediation Strategy
To secure the application against unprotected admin functionality:
1. **Robust Server-Side Authorization:** Enforce rigorous role-based permission checks on the server side for every administrative endpoint rather than relying on unlinked paths or security by obscurity[cite: 6].
2. **Centralized Access Control:** Implement centralized access control middleware to intercept requests and block unauthenticated or low-privilege users from reaching sensitive controllers.
