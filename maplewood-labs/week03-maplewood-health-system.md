# Week 3: Unauthorized USB Drive in Radiology

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** September 14, 2026  
**Analyst:** Inka Salinas 
**Incident ID:** INC-2026-0914-001

---

## Incident Summary

An unmarked USB drive was discovered plugged into workstation MHS-RAD-WS-03 in the restricted Radiology imaging suite. The device was removed, documented, tagged, and transferred to SOC custody without being connected to another system.

---

## Chain of Custody

Chain of custody is important because it documents who handled the evidence, what actions were taken, and when each transfer occurred. The USB was removed by the facilities technician, transferred to IT, logged and tagged as MHS-USB-2026-0913-001, and then transferred to SOC custody without being mounted or examined.

---

## Key Encoding Finding

**String Found:** Y3VybCAtcyAtbyAvZGV2L251bGw=

**Encoding Type:** Base64

**Decoded Content:** curl -s -o /dev/null

**Significance:** The decoded string identifies a curl command, but the fragment alone does not prove that the command was executed, what destination was contacted, or whether a connection was successful. Additional forensic evidence would be required to determine its significance.

---

## Terminal Commands Used

| Command | Purpose |
|---------|---------|
| `echo "unauthorized access" \| base64` | Demonstrates how plain text can be represented using Base64 encoding. |
| `echo "Y3VybCAtcyAtbyAvZGV2L251bGw=" \| base64 -d` | Demonstrates Base64 decoding and reveals the text `curl -s -o /dev/null`. |
| `xxd .bashrc \| head -6` | Displays the first six lines of a hexadecimal representation of the `.bashrc` file. |
| `strings .bashrc \| grep -i "path\|export\|alias"` | Extracts readable strings and filters for potentially relevant configuration terms. |

---

## Escalation Recommendation

I would escalate the incident to Tier 2 for deeper investigation because an unauthorized, unmarked USB drive was found connected to a restricted clinical workstation with access to PACS and the Maplewood EHR. The contents of the drive remain unknown, and the available network monitoring does not eliminate the possibility of activity because overnight monitoring coverage was limited. Questions that require further investigation include who connected the device, whether files were accessed or executed, and whether any information or systems were affected.

---

*CPSC 4584 | Governors State University | Fall 2026*
