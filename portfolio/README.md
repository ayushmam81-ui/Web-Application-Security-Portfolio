# 📂 Ayushma M - Security Project & Lab Portfolio

Welcome to the project and laboratory documentation sector of my security portfolio. This directory houses structured breakdowns of practical security assessments, vulnerability research, and penetration testing exercises.

---

## 🧭 Portfolio Navigation & Case Studies

| Category | Focus Areas / Labs Covered | Description |
| :--- | :--- | :--- |
| **[Access Control & VAPT](#1-access-control--vulnerability-assessments)** | IDOR, Privilege Escalation, Header Overrides (`X-Original-URL`), `robots.txt` | Documenting vertical and horizontal access control bypasses using Burp Suite. |
| **[Business Logic Flaws](#2-business-logic--workflow-manipulation)** | Negative Boundary Values, Cart Limit Bypasses, Coupon Stacking | Analyzing design and logic errors in e-commerce applications. |
| **[CSRF & Session Audits](#3-csrf--session-handling-analysis)** | Anti-CSRF Token Bypass, SameSite Cookies, State-Changing Requests | Evaluating session integrity and cross-site request forgery mechanics. |

---

## 1. Access Control & Vulnerability Assessments
* **Overview:** Detailed write-ups demonstrating how attackers exploit weak conditional authorization, predictable parameters (`roleid`), and misconfigured perimeter headers (`X-Original-URL`) to gain unauthorized administrative access.
* **Key Tools:** Burp Suite (Repeater, Interceptor), Kali Linux.
* **Defensive Focus:** Implementing strict Server-Side Role-Based Access Control (RBAC) and robust session validation routines.

## 2. Business Logic & Workflow Manipulation
* **Overview:** Practical walkthroughs targeting flawed assumptions in software workflows. This section covers manipulating transactional flows by injecting negative boundary values into quantities and bypassing validation checks.
* **Key Tools:** Burp Suite Proxy, Custom payload parameters.
* **Defensive Focus:** Enforcing server-side input boundaries, atomic transaction validations, and strict sequencing logic.

## 3. CSRF & Session Handling Analysis
* **Overview:** Documentation on auditing state-changing application requests that lack cryptographic nonces or proper cookie flags.
* **Key Tools:** Burp Suite, Browser Developer Tools.
* **Defensive Focus:** Integrating unique anti-CSRF tokens per session and enforcing `SameSite=Strict/Lax` cookie attributes.

---
*Return to the main repository summary via the [Root README](../README.md).*
