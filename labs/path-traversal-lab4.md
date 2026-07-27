⚠️ **DISCLAIMER / EDUCATIONAL PURPOSES ONLY**
The information, methodologies, and techniques documented in this write-up are intended solely for educational, training, and authorized security testing purposes. This analysis was conducted within a strictly controlled, legally authorized simulation environment provided by the PortSwigger Web Security Academy. Unauthorized testing, manipulation, or exploitation of live, production web applications without explicit prior consent from the system owner is illegal and punishable under cyber crime laws (including the Information Technology Act in India). The author assumes no liability for the misuse of this information.

***

# Lab Write-Up: Path Traversal with URL Encoding Bypass

### Portfolio Information
* **Author:** Ayushma M
* **Main Repository:** [github.com/ayushmam81-ui/Web-Application-Security-Portfolio](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio)
* **Direct File Link:** [labs/path-traversal-lab4.md](https://github.com/ayushmam81-ui/Web-Application-Security-Portfolio/blob/main/labs/path-traversal-lab4.md)

---

### 1. Target & Scenario
* **Platform:** PortSwigger Web Security Academy
* **Vulnerability Class:** Path Traversal (Directory Traversal) with Encoding Filters[cite: 4]
* **Objective:** Read restricted files when web servers or input filters automatically clean up or block standard relative traversal sequences (`../`) in URLs[cite: 4].

---

### 2. Analysis & Methodology

#### Step 1: Initial Assessment & Identification of Constraints
* Web servers often automatically clean up or reject dangerous literal inputs like `../` found in URLs and file uploads[cite: 4].
* Standard input filters look for plain-text traversal sequences to prevent path traversal attacks.

#### Step 2: Intercepting and Analyzing the HTTP Traffic
* Captured the request using Burp Suite to observe how the application handles parameter input filtering.
* Identified that encoding characters can disguise the traversal payload so it passes initial filters before being decoded by the server backend.

#### Step 3: Manipulation & Successful Exploitation
* Applied URL encoding techniques to hide the traversal characters[cite: 4]:
  * `%2e%2e%2f` as a URL-encoded version of `../`[cite: 4]
  * `%252e%252e%252f` as a double URL-encoded version[cite: 4]
* Explored non-standard encodings like `..%c0%af` or `..%ef%bc%8f` to bypass filters that only understand standard speech, allowing the request to slip through to the server backend[cite: 4].

---

### 3. Remediation Strategy
To secure the application against encoding-based path traversal:
1. **Decode Before Validation:** Ensure all input decoding happens completely *before* applying security filters and safety checks so that encoded evasion attempts are caught.
2. **Canonicalization Checks:** Implement robust path canonicalization to resolve all relative and encoded paths down to their absolute forms before verifying they stay inside the permitted base directory.
