# Week 5: Weak Password Policy Exposes Billing Portal
**Course:** CPSC 4584 | Special Topics in Information Security
**Date:** September 28, 2026
**Analyst:** Inka Salinas
**Incident ID:** 1544103

---

## Incident Summary

The Maplewood billing portal experienced a credential stuffing attack involving 847 failed authentication attempts from 12 different IP addresses over a 72-hour period. The attacker eventually successfully authenticated to the billing_admin_03 account using a compromised password and accessed insurance records for 3,247 unique patients, including names, dates of birth, insurance policy numbers, and claim histories during a 47-minute session.

---

## TLS Assessment

**TLS Version:** TLS 1.3  
**Status:** Compliant  
**What TLS Protected:** Data in transit between the client and server  
**What TLS Did Not Protect:** Authentication -- the attacker had valid credentials

TLS 1.3 provided encryption and protection for data transmitted between the client and the billing portal. However, TLS could not determine whether the person using the valid credentials was the legitimate account owner. Because the attacker possessed the compromised password, the secure TLS connection did not prevent the unauthorized authentication. Additional authentication controls such as MFA were needed to provide another layer of protection.

---

## Authentication Controls Gap Analysis

| Control | Required | Status | Finding |
|---------|----------|--------|---------|
| MFA | Maplewood sensitive-account standard | Not Implemented | MFA was not enabled for the billing administrator account, allowing the compromised password to be used as the only authentication factor. |
| Failed Attempt Protection | Account-based throttling and alerting | Not Implemented | The account experienced 847 failed authentication attempts from 12 IP addresses without effective account-based throttling or alerting to stop or slow the repeated attempts. |
| Password Policy | NIST SP 800-63B-4 aligned | Needs Improvement | The password policy required a minimum of 12 characters but did not screen passwords against known compromised credentials. The compromised password was therefore able to be reused against the billing account. |
| Compromised Credential Response | Detect and invalidate confirmed compromised authenticators | Not Implemented | There was no documented control in place to detect and invalidate a confirmed compromised password before it was successfully used against the billing account. |
| Automated Attack Controls | Throttling, bot detection, or adaptive controls as appropriate | Not Implemented | The distributed authentication attempts from multiple IP addresses were not effectively detected or prevented through automated attack controls. |

---

## OpenSSL Commands Practiced

| Command | Purpose |
|---------|---------|
| `openssl genrsa -out private_key.pem 2048` | Created a 2048-bit RSA private key and saved it to the `private_key.pem` file. |
| `openssl rsa -in private_key.pem -pubout -out public_key.pem` | Derived the corresponding RSA public key from the private key and saved it to the `public_key.pem` file. |
| `openssl rsa -in private_key.pem -text -noout \| head -20` | Displayed the first 20 lines of the RSA private-key information, including the 2048-bit key size and modulus, without displaying the PEM-encoded private key itself. |
| `cat public_key.pem` | Displayed the PEM-formatted public key stored in the `public_key.pem` file, including the `BEGIN PUBLIC KEY` and `END PUBLIC KEY` markers. |

---

## Escalation Summary

The confirmed findings show that the Maplewood billing portal was accessed through a successful authentication after 847 failed login attempts originating from 12 IP addresses. The billing_admin_03 account did not have MFA, account-based failed-attempt protection, automated attack controls, or a compromised-credential response process. The password policy also did not prevent the use of a known compromised credential. During the successful 47-minute session, the account accessed records belonging to 3,247 unique patients.

TLS 1.3 was functioning as expected and protected data in transit; however, TLS did not prevent the compromise because the attacker possessed valid credentials. Authorized leadership should determine the required credential reset and account-containment actions, confirm the complete scope of records accessed, determine whether information was downloaded or exfiltrated, identify the source of the compromised credential, and determine whether other accounts were targeted. Any permanent authentication-control changes should be reviewed and implemented through the organization's authorized security and change-management processes.

---
*CPSC 4584 | Governors State University | Fall 2026*
