# Week 6: Deleted Files on a Terminated Employee's Laptop

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** October 5, 2026  
**Analyst:** Inka Salinas
**Case ID:** FOR-2026-1005-001

---

## Incident Summary

Maplewood Health System failed to collect a billing coordinator's laptop at termination, leaving the device outside organizational custody for approximately 43 hours. After the device was returned, forensic analysis identified file deletion events and recovered five artifacts, including potentially sensitive insurance and billing-related files, a vendor contact document, a personal image, and a partially recovered temporary file.

---

## Chain of Custody Status

**Gap Period:** Oct 3, 2:47 PM to Oct 5, 9:30 AM (approximately 43 hours)

**Gap Significance:** The custody gap limits the ability to independently verify who had access to the laptop and what activity occurred while it was outside organizational control. Although deletion events were recorded during this period, the available information does not independently establish who performed the deletions, why they occurred, or whether information was disclosed. The gap does not automatically invalidate the evidence, but it requires transparent documentation, careful preservation, and corroboration with other forensic artifacts.

**Documented By:** Analyst on receipt of device

---

## Recovered File Evidence Classification

| Filename | Type | Evidentiary Value | Finding |
|---|---|---|---|
| `insurance_export_final.xlsx` | Spreadsheet | High investigative priority | The filename suggests possible insurance-related information that may include sensitive data. Its contents, access history, and relationship to authorized job duties should be examined before determining whether it contains protected information. |
| `mhs_billing_schema_db.sql` | SQL script | High investigative priority | The filename suggests a possible connection to the billing database schema. Review may establish whether the file contains database structures or other sensitive technical information and whether its presence was authorized. |
| `vendor_contact_list_external.docx` | Document | Moderate to high investigative priority | The filename suggests vendor contact information and possible external-sharing relevance. The document's contents, ownership, and authorized business purpose require verification; the filename alone does not establish data exfiltration. |
| `__tmp_8f2a.dat` | Partial artifact | Limited; further analysis required | Only 22 KB of an estimated 400 KB was recovered. The incomplete data limits interpretation and may prevent reliable identification of the file's original contents or purpose. Additional recovery attempts may be warranted. |
| `personal_vacation_2025.jpg` | Image | Context-dependent | The filename suggests a personal vacation image. Its presence may provide contextual information, but it does not independently demonstrate misconduct or a connection to the incident. |

These classifications reflect investigative priority rather than proof of unauthorized activity. Final assessments require examination of the recovered contents, file metadata, applicable policies, and corroborating evidence.

---

## Metadata Inspection Commands Used

| Command | Purpose |
|---|---|
| `stat .bashrc` | Inspects file metadata, including size, permissions, ownership, inode number, and access, modification, and status-change timestamps. |
| `ls -lai` | Lists directory entries, including hidden files, and displays inode numbers and file details. Inodes help identify filesystem objects, but do not independently establish a file's deletion history. |
| `xxd .bashrc \| head -3` | Displays the first three lines of a hexadecimal dump. The first bytes, `23 20`, represent the readable ASCII characters `# `. The output begins with a Bash configuration comment rather than a recognizable binary file signature. The `Broken pipe` message occurred because `head` stopped reading after three lines and closed the pipe. |
| `find . -newer .bashrc -type f` | Searches the current directory tree for regular files with modification timestamps newer than `.bashrc`. The output included `README.txt`, `.ssh/known_hosts`, `encryption.py`, `private_key.pem`, `password.enc`, `traces.zip`, numerous trace files, and files under `evidence/`. The results identify files for possible timeline review but do not prove suspicious activity. |

**Forensic interpretation:** Metadata and timestamp-based searches can help establish investigative leads, but their results must be interpreted in context. The commands above were executed against the practice environment. Their output is not direct evidence from the recovered Maplewood laptop.

---

## Escalation Summary

The investigation identified an approximately 43-hour custody gap, operating system deletion events during that period, and five recovered artifacts. According to the case record, a forensic image was created using FTK Imager, SHA-256 hash verification was completed, and Foremost was used to recover the files. Four artifacts were reportedly recovered completely, while the temporary file was only partially recovered.

The insurance spreadsheet and billing SQL script warrant prioritized review because of their potential relationship to sensitive insurance information and billing infrastructure. The vendor contact document should also be examined to establish its contents and authorized business purpose. The partially recovered temporary file requires cautious interpretation because most of its estimated contents were not recovered.

The current findings do not independently establish who deleted the files, whether the files were accessed or transferred by an unauthorized person, or whether a policy violation occurred. Recommended next steps include preserving the forensic image and logs, documenting the custody gap and evidence-handling procedures, validating relevant hashes, and correlating file metadata with system logs and other available artifacts.

The findings should be escalated to the supervisor and authorized HR, information security, and legal reviewers for a determination of appropriate follow-up. Any inconsistency in the employee's identity or incident timeline should also be resolved before the final report is issued.

---

*CPSC 4584 | Governors State University | Fall 2026*
