# Week 4: Unencrypted Patient Records on a Shared Drive
**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** September 21, 2026  
**Analyst:** Inka Salinas
**Audit ID:** AUD-2026-0921-001

---

## Incident Summary

During a routine IT infrastructure audit, Maplewood Health System discovered a shared folder named PATIENT_DATA_ARCHIVE on a clinical file server containing 847 Excel and CSV files with 8,247 unique patient records. The records contained sensitive PHI, including patient names, dates of birth, Social Security numbers, diagnosis codes, and insurance information, and were stored without encryption while broad read and write access was available to authenticated users across the clinical network.

---

## HIPAA Compliance Assessment

| Requirement | Status | Finding |
|-------------|--------|---------|
| Encryption at Rest | REQUIRES REVIEW | HIPAA treats encryption as an addressable implementation specification, meaning Maplewood must evaluate whether encryption is reasonable and appropriate for its environment, document its determination, and consider an equivalent safeguard when appropriate. In this scenario, plaintext PHI combined with broad access creates significant security risk. |
| Access Controls | CONTROL FAILURE | Read and write access to the archived PHI was available to all authenticated users on the clinical network segment, including clinical staff, administrative employees, and IT personnel across four clinic locations and the inpatient facility, rather than being limited according to job responsibilities. |
| Audit Controls | CONTROL FAILURE | No access logs were retained for the folder before discovery. As a result, Maplewood cannot reconstruct who accessed the archived PHI or when the access occurred. |
---

## Cryptographic Controls Evaluated

**Base64 encoding:** Base64 is an encoding method, not encryption. It changes the representation of data but does not provide confidentiality or require a secret cryptographic key. Base64-encoded information can be readily decoded back to its original form.

**Caesar cipher:** A Caesar cipher is a basic substitution technique that shifts characters by a fixed number of positions. It is not an appropriate modern cryptographic control because it provides extremely weak protection and can be easily defeated. It should not be treated as acceptable encryption for protecting ePHI.

**Modern encryption at rest:** Maplewood should evaluate a recognized modern encryption approach for stored ePHI, including appropriate encryption algorithms and secure key management. Encryption at rest would provide an additional layer of protection by making stored information unreadable without the appropriate decryption key if unauthorized individuals obtained the underlying files or storage.

---

## Hashing Commands Practiced

| Command | Purpose | Output Length |
|---------|---------|---------------|
| `echo -n "patient record" \| sha256sum` | Demonstrates how SHA-256 creates a fixed-length cryptographic digest that can be used as an integrity baseline. | 64 hexadecimal characters |
| `echo -n "patient record" \| md5sum` | Demonstrates the difference between MD5 and SHA-256 and shows why algorithm selection matters when evaluating cryptographic integrity controls. | 32 hexadecimal characters |
| `sha256sum .bashrc` | Demonstrates how hashing an actual file can establish an integrity baseline that can later be compared to determine whether the file has changed. | 64 hexadecimal characters |

### Practice Hash Outputs

**SHA-256 of "patient record":**  
`22e788497d8512ac07a4ad20cbb1b40e08200a265d59bb9494e504bcfed1f91d`

**MD5 of "patient record":**  
`c604e89200b64753354653a2603ac64a`

**SHA-256 of .bashrc:**  
`342099da4dd28c394d3f8782d90d7465cb2eaa611193f8f378d6918261cb9bb8`

---

## Escalation Summary
---

## Escalation Summary

The confirmed finding is that Maplewood stored 8,247 unique patient records containing sensitive PHI in 847 files without encryption at the file, folder, or disk level. The archived information was accessible with read and write permissions to a broad population of authenticated users across the clinical network. Additionally, access records were not retained, preventing investigators from reconstructing historical access to the folder. The folder was discovered during the September 21, 2026 audit and was subsequently access-restricted while the investigation continued. The scope of any unauthorized access remains unknown. Leadership, privacy, security, and legal personnel must review the finding and determine the appropriate response, including whether the circumstances constitute a reportable breach and what remediation or notification requirements apply. As a Tier 1 analyst, I should not conclude that a reportable breach occurred because unauthorized access has not been established from the available evidence.

---

*CPSC 4584 | Governors State University | Fall 2026*
