# Week 9: SQL Injection Attempt on Appointment Scheduling System

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** October 26, 2026  
**Analyst:** Inka Salinas  
**Case ID:** SQLI-2026-1024-001

---

## Incident Summary

On October 24, 2026, six search inputs were submitted to Maplewood Health System’s appointment scheduling system between 02:14:07 and 03:01:22. The inputs progressed from a normal surname search to SQL injection patterns, ending with an attempt to reference patient names, dates of birth, and Social Security numbers; however, the available access logs do not confirm that patient data was successfully retrieved.

---

## SQL Injection Log Analysis

| Entry | Time | Decoded Input | Type | Severity |
|---|---|---|---|---|
| 1 | 02:14:07 | `Johnson` | Benign Input | Info |
| 2 | 02:14:52 | `'` | Quote Probe | Medium |
| 3 | 02:18:31 | `Smith' --` | Comment Syntax Test | High |
| 4 | 02:23:14 | `' OR '1'='1` | Tautology Pattern | High |
| 5 | 02:48:39 | `' UNION SELECT NULL,NULL,NULL --` | UNION Injection Attempt | Critical |
| 6 | 03:01:22 | `' UNION SELECT patient_name,date_of_birth,ssn FROM patients --` | Targeted PHI Extraction Attempt | Critical |

**Evidence limitation:** These classifications describe the submitted input patterns and their potential purpose. The access logs do not establish whether the database executed the inputs successfully, returned patient records, or disclosed protected health information (PHI).

---

## Attack Progression Summary

**Entry 1 — Baseline Input:** The value `Johnson` is an ordinary surname search. It establishes a baseline for expected input and does not, by itself, indicate malicious activity.

**Entry 2 — Quote Probe:** A single quote can test how an application handles SQL-special characters and whether unsafe query construction may produce an error or unexpected behavior. The log alone does not show how the application responded.

**Entry 3 — Comment Syntax Test:** The input `Smith' --` combines a quote with SQL comment syntax. In some SQL contexts, a comment marker can cause the remainder of a statement to be ignored, potentially changing the intended query. Whether it affected the application's query is unconfirmed.

**Entry 4 — Tautology Pattern:** The input `' OR '1'='1` contains a condition that can evaluate as true when interpreted as SQL in the relevant context. If unsafe concatenation allows the input to alter a query's logic, it could broaden results beyond the intended surname match. The available access log does not prove that this occurred.

**Entry 5 — UNION Column-Shape Probe:** The `UNION SELECT NULL,NULL,NULL` pattern appears designed to test whether a UNION-based query can be combined with the original query and whether a three-column result shape is compatible. The log does not confirm successful execution or returned data.

**Entry 6 — Targeted Patient-Data Attempt:** The input names `patient_name`, `date_of_birth`, and `ssn` from a `patients` table, suggesting an attempt to retrieve specific patient information and possible knowledge of the database schema. However, the source, authorization, execution outcome, and any data returned require verification.

---

## Remediation Summary

**Primary Application Control — Parameterized Queries:** Replace direct SQL string concatenation with PHP PDO prepared statements and bound parameters. Parameterization separates SQL instructions from user-supplied values so that input is treated as data rather than executable SQL syntax. The application team should review the affected search path and related database interactions.

**Input Handling — Validation:** Validate the surname field against the expected format and length for the application, and reject malformed values when appropriate. Removing single quotes or filtering selected characters is not a reliable primary defense because SQL injection attempts can use different syntax or encodings. Input validation should complement, not replace, parameterized queries.

**Database Permissions — Least Privilege:** Review the scheduling application's database account and reduce its permissions to the minimum needed for its business function. The incident brief states that the account has `SELECT` permission on both the appointments and patients tables. The DBA should determine whether access to patient records is necessary for appointment lookup and restrict unnecessary access.

**Detection and Monitoring — Defense in Depth:** Preserve and correlate web access logs, application error logs, database query logs, and relevant server-side telemetry. Add alerting for repeated SQL-special-character probes, tautology patterns, and UNION-based inputs. Evaluate deploying a properly configured Web Application Firewall (WAF), since the incident brief states that no WAF is currently in place. These controls can improve detection and reduce risk, but they do not replace secure query construction.

---

## Escalation Summary

The SOC identified six search inputs submitted over a 47-minute period on October 24, 2026. Entries 2–6 contain patterns consistent with SQL injection testing, and Entry 6 specifically references patient names, dates of birth, and Social Security numbers. The incident brief also states that the PHP application passes user input directly into SQL without parameterization, that the database account can read both the appointments and patients tables, and that no WAF is in place. These are documented risk conditions; successful exploitation and PHI disclosure are not confirmed.

The SOC should preserve relevant evidence and escalate through the approved incident response process. The DBA should review database query logs, execution results, errors, and available records of returned rows. The application team should remediate unsafe query construction and review related code paths and database permissions. The supervisor should coordinate with privacy and legal reviewers after the technical evidence has been assessed. No conclusion that PHI was accessed or disclosed should be made from the web access log alone. Any disclosure determination should be based on corroborated server-side evidence and the organization's incident response procedures.

---

*CPSC 4584 | Governors State University | Fall 2026*
