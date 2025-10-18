# Log Analysis — Privilege Escalation — Writeup

**Challenge:** Log Analysis - Privilege Escalation  
**Context:** A server containing sensitive data accessible only to `root` was found leaked. Responders report that remote web access would be via `www-data` (which should not have access). The dev claims upload filters prevent PHP uploads. Bash history appears unrelated. Your task: determine what actually happened from provided logs.

---

## TL;DR
The attacker uploaded a web shell using a file extension that bypassed the developer’s filter (`.phtml`), ran reconnaissance tools (tried to download `linux-exploit-suggester.sh` and use `tcpdump`), and escalated to `root` by abusing a misconfigured binary with the SUID bit set (SUID exploitation). The only non-root user present on the box is `daniel`.

---

## Quick methodology
1. Inspect the provided log file and bash history for anomalies (uploaded filenames, downloaded scripts, invoked tools).  
2. Search for evidence of file upload names and unusual extensions (look for `.phtml`, `.php`, etc.).  
3. Correlate commands indicating downloads or attempted reconnaissance (`wget`, `curl`, `tcpdump`, `linux-exploit-suggester.sh`).  
4. Identify the escalation vector from the sequence of commands run just before removing the web shell (look for use of SUID binaries or library preloading exploits).

---

## Key findings
- A **non-root user** `daniel` exists on the server (logged in / present on system).  
- The attacker attempted to download a known exploit reconnaissance script named **`linux-exploit-suggester.sh`** (likely to find local privilege escalation vectors).  
- The attacker tried to use **`tcpdump`** as a packet analyzer during recon.  
- To bypass the developer’s upload filter, the attacker uploaded their web shell using the **`.phtml`** extension (commonly executed by PHP-aware webservers while sometimes overlooked by naive filters).  
- Privilege escalation occurred by exploiting a **SUID** misconfiguration on a binary (SUID set), which allowed the attacker to escalate from the web shell user to `root`.

---

## Answers (direct)
- **What user (other than ‘root’) is present on the server?**  
  `daniel`

- **What script did the attacker try to download to the server?**  
  `linux-exploit-suggester.sh`

- **What packet analyzer tool did the attacker try to use?**  
  `tcpdump`

- **What file extension did the attacker use to bypass the file upload filter implemented by the developer?**  
  `.phtml`

- **Based on the commands run by the attacker before removing the php shell, what misconfiguration was exploited in the ‘python’ binary to gain root-level access?**  
  `4` (SUID)

---

## Detection & remediation recommendations
- **Harden upload handling:** validate file contents (MIME sniffing), not just extensions; whitelist allowed types and block execution in upload directories.  
- **Detect uploads with suspicious extensions:** alert on `.phtml`, `.php5`, or other executable extensions appearing in upload folders.  
- **Monitor for web shell indicators:** POST requests with file-upload patterns, calls to `cmd` parameters, and HTTP multipart uploads.  
- **SUID inventory & hardening:** regularly scan for SUID binaries (`find / -perm -4000 -type f`) and remove SUID where unnecessary; patch or replace vulnerable SUID programs.  
- **Limit tool availability:** restrict `tcpdump`, `wget`, `curl`, `python` usage to administrative accounts via sudoers and monitoring.  
- **Logs correlation:** correlate webserver access logs with uploads and subsequent command execution to speed triage.

---

## Useful commands for investigation
- Find SUID binaries:
  ```bash
  find / -perm -4000 -type f 2>/dev/null
