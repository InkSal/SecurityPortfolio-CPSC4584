# Week 2: Suspicious File on a Nurse's Workstation

**Course:** CPSC 4584 | Special Topics in Information Security  
**Date:** September 9, 2026  
**Analyst:** Inka Salinas  
**Case:** Maplewood Health System — Suspicious File on a Nurse's Workstation

---

## Incident Brief

This section is read-only reference material in the lab submission. The written analysis and terminal investigation log below document the analyst's assessment and the commands practiced in the lab environment.

---

## Written Analysis

### File System Location and Significance

The `/home/nurse01/` directory matters because it is the home directory associated with the assigned clinical user account. Files stored there may be accessible to that user and may reflect activity performed under that account. However, the location alone does not prove that the nurse created the file because another process, a compromised account, or unauthorized activity could have placed it there.

I would next examine other files in `/home/nurse01/` for unexpected activity and `/tmp` for recently created temporary files, looking for unusual filenames, permissions, timestamps, or other related artifacts.

### Permission String Analysis

The permission string `-rwxr--r--` identifies the item as a regular file. The owner has read, write, and execute permissions, while the group has read-only permission and other users also have read-only permission.

The owner execute bit is unusual for a file named `patient_notes.txt` because a document presented as a text file would not normally need execute permission. This raises questions about whether the file is actually a text file, whether its permissions were changed, and whether it contains unexpected or executable content. I would verify the file type, ownership, metadata, timestamps, and contents before making any conclusion about its purpose or intent.

### Analyst Response and Escalation Decision

I would escalate this alert to a Tier 2 analyst because several indicators require deeper investigation. The file appeared at 3:14 AM outside normal clinical activity, the assigned nurse denies placing it on the workstation, no scheduled task or automated process is known to have created it, and the file has an unusual owner execute permission. The workstation is also connected to the clinical network and has access to the EHR system.

My escalation would include the workstation name, file name and location, timestamp, permission string, nurse statement, and the fact that no authorized process explains the file. Tier 2 still needs to determine who or what created the file, its actual type and contents, whether related files exist, and whether other systems were affected.

The Section 3 commands demonstrate how an analyst can safely collect metadata, file-type information, readable strings, and related-file information without executing the suspicious file.

---

## Terminal Investigation Log

### Terminal Entry 1 — Working Directory and Directory Listing

**Command**

```bash
pwd && ls -la
```

**Output**

```text
/home/isal-academy
total 44
drwxr-xr-x 5 isal-academy isal-academy 165 Sep 9 01:50 .
drwxr-xr-x 3 root         root          54 Sep 2 03:30 ..
-rw-r--r-- 1 isal-academy isal-academy 220 Sep 2 03:30 .bash_logout
-rw-r--r-- 1 isal-academy isal-academy 3771 Sep 2 03:30 .bashrc
-rw-rw-r-- 1 isal-academy isal-academy 1024 Sep 9 01:50 .get_flag.sh.swp
drwxrwxr-x 3 isal-academy isal-academy 19 Sep 9 01:50 .local
-rw-r--r-- 1 isal-academy isal-academy 807 Sep 2 03:30 .profile
drwx------ 2 isal-academy isal-academy 48 Sep 4 00:40 .ssh
-rw-r--r-- 1 root root 4510 Sep 9 17:03 README.txt
-rw-rw-r-- 1 isal-academy isal-academy 0 Sep 9 01:50 flag.txt
drwxrwxr-x 3 isal-academy isal-academy 33 Sep 9 01:42 pico
-rwxrwxr-x 1 isal-academy isal-academy 19312 Dec 12 2025 warm
```

**Analyst Observation**

The `pwd` command shows that my current location is `/home/isal-academy`. The `ls -la` command lists the files and directories in that home directory, including hidden files. I identified `README.txt` as a readable file owned by root with read permissions for the owner, group, and other users. I selected `README.txt` for Entries 2 through 4 because it is an existing file that can be examined with passive commands such as `file`, `stat`, and `strings` without executing or modifying it.

### Terminal Entry 2 — File Type

**Command**

```bash
file README.txt
```

**Output**

```text
README.txt: ASCII text, with escape sequences
```

**Analyst Observation**

The `file` command reports that `README.txt` is an ASCII text file with escape sequences. This result matches the `.txt` filename extension because the file is identified as a type of text file. An analyst verifies the actual file type before deciding how to handle a suspicious file because a filename or extension can be misleading. Confirming the file type helps prevent incorrect assumptions and supports a safer investigation.

### Terminal Entry 3 — File Metadata

**Command**

```bash
stat README.txt
```

**Output**

```text
File: README.txt
Size: 4510            Blocks: 16            IO Block: 4096 regular file
Device: 10301h/66305d Inode: 2223605216 Links: 1
Access: (0644/-rw-r--r--) Uid: (0/root) Gid: (0/root)
Access: 2026-09-02 03:30:51.724152801 +0000
Modify: 2026-09-09 17:03:52.497013255 +0000
Change: 2026-09-09 17:03:52.497013255 +0000
Birth: 2026-09-02 03:30:51.724152801 +0000
```

**Analyst Observation**

The Access timestamp is September 2, 2026 at 03:30:51 UTC. The Modify and Change timestamps are both September 9, 2026 at 17:03:52 UTC. A Birth timestamp is also present and is September 2, 2026 at 03:30:51 UTC. Because the Modify and Change times are identical, there is no difference between them in this output. If they were different, it could suggest that the file's contents and its metadata or filesystem information were changed at different times. However, timestamps alone would not prove malicious activity because legitimate users, programs, or system processes can create or modify files and their metadata.

### Terminal Entry 4 — Readable Strings

**Command**

```bash
strings README.txt | head -20
```

**Output**

```text
[1mWelcome to the CyLab Security Academy webshell!
[1mUse the arrow keys or spacebar to scroll, or type
[1;36mq
[1;37m to exit.
This is a browser-accessible Linux shell that can be used for solving
CyLab Security Academy challenges.
Note that using the webshell is not necessary for solving challenges.
All files and programs are either available for download or accessible
via remote ports. It is intended primarily for users who do not
have access to their own local shell environment, such as students using
school-provided hardware.
The webshell has many common tools for solving CTF challenges available.
Due to restrictions in the webshell, it is generally not possible to
install additional software. However, if there is a specific tool that
you feel would be helpful to include, send a note on Discord or to
support@cylabacademy.org and we will consider adding it.
There are various restrictions in place to prevent abuse of the webshell.
Some resource limits can be checked by typing
[0;36musage
[0m. Note that all
```

**Analyst Observation**

The output shown contains the CyLab Security Academy webshell welcome and usage instructions rather than readable strings from the file I examined. Therefore, I cannot identify a file-specific string from this output without additional command results. The `strings` command is useful because it can reveal readable text embedded in a file without executing it, which may provide clues about a file's purpose or contents. However, readable strings alone do not prove what a program will do or establish malicious intent.

### Terminal Entry 5 — Recently Modified Files

**Command**

```bash
find . -mtime -1 -type f
```

**Output**

```text
./README.txt
./flag.txt
./.get_flag.sh.swp
```

**Analyst Observation**

The command returned three files from the current directory tree: `./README.txt`, `./flag.txt`, and `./.get_flag.sh.swp`. A time-based search helps an analyst identify files that were recently created or modified and may be related to activity around an incident. The unexpected presence of `flag.txt` and `.get_flag.sh.swp` should be documented and examined further because they may indicate related activity or artifacts. I would next document the filenames and paths, review their timestamps and metadata with `stat`, verify their file types with `file`, and determine whether they are related to the incident.

---

## Escalation Summary

The suspicious file requires escalation because it appeared at an unusual time, the assigned nurse denied placing it on the workstation, no authorized automated process was known to explain its creation, and its permissions included an execute bit despite the `.txt` filename. The workstation's connection to the clinical network and access to the EHR system increase the importance of a careful investigation.

The next steps are to preserve relevant evidence, verify the file's type and metadata, investigate related files and activity, and determine whether other systems were affected. The available information raises investigative concerns but does not independently establish who created the file or prove malicious intent.

---

## Portfolio Submission

**Repository:** [CPSC4584-SecurityPortfolio](https://github.com/InkSal/SecurityPortfolio-CPSC4584)

**Suggested path:** `maplewood-labs/week02-suspicious-file.md`

---

*CPSC 4584 | Governors State University | Fall 2026*
