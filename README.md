# Linux Root Environment Security Project

**Objective:** Secure the root account and implement controlled administrative access.

**Presented by:** Belfaida Barae, El Hami Yassine  
**Supervisor:** Pr. Boughanja Manale

---

## Overview

The root account is the super-administrator of Linux/Unix systems, with unlimited privileges to control the system.

While powerful, this access also represents a critical risk if misused or compromised.

Securing the root environment is essential to ensure the **integrity, confidentiality, and availability** of the system.

---

## Critical Implications of Root Access

- Full read/write/delete privileges for all files  
- Installation and removal of software  
- Complete user and service management  

**Risks include:**  

- Data theft or destruction  
- Server downtime  
- Service disruption and financial/reputational impact  

**Conclusion:** Root access must be tightly controlled and monitored.

---

## Four Pillars of Security

1. **Least Privilege** – Grant only the necessary permissions to minimize damage if an account is compromised.  
2. **Traceability & Auditing** – Log all sensitive actions to identify responsible users or mistakes.  
3. **User Responsibility** – Train users to avoid errors and social engineering attacks.  
4. **Attack Surface Reduction** – Remove unnecessary services and ports to reduce vulnerabilities.

---

## Access Control & Restrictions

To strengthen root security:  

- **Disable direct root access** – Block interactive and SSH login as root.  
- **Controlled privilege escalation** – Use `sudo` or `su` only when necessary.  
- **Session management** – Automatically log out inactive root sessions.  
- **Protect critical services** – Limit access to boot processes and sensitive services.  

*Source: Red Hat Enterprise Linux Security Guide*

---

## Implementation

### 1. Disable direct root login

- Lock root password:  
  ```bash
  passwd -l root
