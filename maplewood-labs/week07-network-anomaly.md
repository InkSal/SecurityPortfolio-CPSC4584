# Week 7: Network Traffic Anomaly During Night Shift

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** October 12, 2026  
**Analyst:** Inka Salinas  
**Case ID:** NET-2026-1012-001 (related: FOR-2026-1005-001)

---

## Incident Summary

The network investigation identified suspicious outbound traffic from Webb's laptop, repeated connections consistent with possible automated beaconing, and unusual DNS and HTTP activity involving other Maplewood workstations. These findings correlate with Week 6 disk evidence showing that `insurance_export_final.xlsx` was modified shortly before the outbound transfer and deleted shortly afterward; however, the available evidence does not independently confirm that the spreadsheet was transmitted.

---

## Network Flow Analysis

| Flow | Source | Destination | Classification | Finding |
|------|--------|-------------|----------------|---------|
| 1 | Webb laptop | 185.220.101.47 | Suspicious outbound transfer; possible data exfiltration | The flow recorded 2,457,814 outbound bytes beginning at 11:48 p.m. on October 3. The destination was unauthorized, making the transfer suspicious. Flow metadata alone cannot establish what data was transmitted. |
| 2 | Webb laptop | 185.220.101.47 | Possible beaconing | Repeated 312-byte connections to the same destination occurred approximately every four minutes from 11:15 p.m. on October 3 through 6:42 a.m. on October 4. The regular timing and consistent size support a beaconing hypothesis, but do not prove command-and-control (C2) activity. |
| 3 | EHR prod server | AWS | Expected baseline traffic | The 14.2 MB HTTPS transfer was associated with the scheduled nightly backup to the approved EHR vendor. The authorized destination and expected business purpose support classifying this traffic as baseline activity. |
| 4 | Radiology WS | 8.8.8.8 | Unusual DNS activity requiring investigation | The workstation generated 23 DNS queries during an unusual period. The query volume and timing warrant review against normal workstation behavior and approved DNS configurations, but do not establish malicious activity. |
| 5 | Billing WS | 203.0.113.99 | Suspicious repeated HTTP traffic; possible beaconing | Nine similar HTTP connections occurred at approximately four-minute intervals to an unapproved destination. The repeated timing and similar connection characteristics are consistent with possible beaconing, but additional evidence is required to confirm C2 activity. |

---

## Disk and Network Evidence Correlation

**Disk (Week 6):** The file `insurance_export_final.xlsx` was approximately 2.4 MB, was last modified at 11:47 p.m. on October 3, and was deleted at 11:52 p.m. The Week 6 investigation also documented an approximately 43-hour chain-of-custody gap, which limits confidence in the continuity of the evidence-handling record.

**Network (Week 7):** Flow 2 showed repeated 312-byte connections from Webb's laptop to 185.220.101.47 beginning at 11:15 p.m. on October 3 and continuing until 6:42 a.m. on October 4. Flow 1 recorded 2,457,814 outbound bytes to the same destination beginning at 11:48 p.m. on October 3.

**Correlation:** The outbound transfer began one minute after the spreadsheet's last-modified timestamp and four minutes before its recorded deletion. The approximate similarity between the file size and the transferred byte count, combined with the close timing, supports investigating whether the spreadsheet was involved in the transfer. The repeated connections also suggest that automated communication may have been occurring before and after the transfer. These relationships are investigative leads, not proof of exfiltration or malware activity.

**Limitation:** Network flow data typically records connection metadata, such as source and destination addresses, ports, timestamps, protocols, and byte counts. It does not necessarily reveal the contents of encrypted traffic or identify a particular file transferred during a session. Without packet captures, endpoint telemetry, application logs, and corroborating file-transfer evidence, the contents of the outbound transfer and the purpose of the repeated connections cannot be confirmed.

---

## Network Artifact Inspection Commands Used

| Command | Purpose |
|---------|---------|
| `curl -I https://example.com` | Requests HTTP response headers without downloading the response body. Record the actual status line and headers returned by the server. These may include content type, caching information, and server-related metadata. |
| `curl -v --max-time 5 https://example.com` | Displays verbose connection information and available TLS and HTTP exchange details, subject to the connection and the five-second timeout. The verbose output can help identify connection progress, request headers, and response headers. |
| `curl -s https://example.com -o /tmp/response.html && xxd /tmp/response.html \| head -4` | Saves the response body and displays its first four lines of hexadecimal and readable-character output. This helps inspect the initial byte sequence and determine whether recognizable text or HTML is present. |
| `curl -s -o /dev/null -w "HTTP: %{http_code} \| Size: %{size_download} bytes \| Time: %{time_total}s \| IP: %{remote_ip}\n" https://example.com` | Reports the HTTP status code, downloaded byte count, total request duration, and remote IP address. These measurements relate to flow concepts such as traffic volume, destination, and timing, although the HTTP request duration may differ from a network flow's recorded duration. |

**Inspection evidence note:** Actual terminal outputs should be recorded separately for each command. The command descriptions above explain the purpose of the tests and do not represent verified execution results. The `example.com` test traffic is for artifact inspection and is not evidence from the Maplewood incident.

---

## Escalation Summary

The findings warrant escalation of NET-2026-1012-001 to the SOC supervisor for coordinated investigation with the related Week 6 case, FOR-2026-1005-001. The confirmed observations include the outbound transfer of 2,457,814 bytes from Webb's laptop to 185.220.101.47, repeated 312-byte connections to the same destination, the spreadsheet's modification and deletion timestamps, unusual DNS activity from the radiology workstation, and repeated HTTP connections from the billing workstation. The EHR production server's scheduled transfer to the approved AWS-hosted vendor provides a useful baseline for comparison.

The close timing and approximate size similarity between the spreadsheet and outbound transfer support further investigation, but do not prove that the file was transmitted. The repeated connections are consistent with possible beaconing, but C2 activity remains unconfirmed. The billing workstation's traffic and the radiology workstation's DNS activity also require additional context before attribution or a determination of compromise can be made. The shared /24 subnet relationship with addresses associated with the Week 5 billing-portal incident is a correlation point, not proof of a common threat actor.

Recommended next steps are to preserve the original network and endpoint evidence, verify timestamps and time zones, correlate firewall, DNS, proxy, authentication, and endpoint logs, and obtain packet captures or other available evidence to identify the nature of the outbound traffic. The documented chain-of-custody gap should be included in the evidence assessment. Any isolation of workstations, firewall changes, formal incident declarations, and threat attribution should follow Maplewood's authorized incident-response procedures and leadership review.

---

*CPSC 4584 | Governors State University | Fall 2026*
