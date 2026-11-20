# Attack scenario and detection notes

The renderer plays a **scripted, simulated** attack. This page describes what each step represents and how a SOC could detect it. The detection ideas are starting points: verify them in your own lab before relying on them.

| # | Step | Technique | Typical log sources | Detection idea |
|---|---|---|---|---|
| 1 | C2 beacon from an external host reaches the firewall | T1071 Application Layer Protocol | Firewall logs, Zeek `conn.log`, proxy logs | Periodic connections with near-constant interval and size to a rare external destination |
| 2 | Web shell uploaded to the Web Server | T1505.003 Web Shell | Web access logs, Sysmon Event ID 1 and 11, WAF | Web server process spawning a shell or system tools; new script files in the web root |
| 3 | Credential dumping on WS-01 | T1003 OS Credential Dumping | Sysmon Event ID 10, Windows Security 4688 | Non-system process accessing `lsass.exe` memory |
| 4 | SMB lateral movement to the File Server | T1021.002 SMB / Windows Admin Shares | Windows 4624 (logon type 3), 5140/5145, Zeek `smb` logs | Workstation-to-server SMB with admin shares from an unusual source or account |
| 5 | Data exfiltration from the DB Server | T1041 Exfiltration Over C2 Channel | NetFlow, Zeek `conn.log`, firewall logs | Large outbound transfer from a database server to a rare external IP |

## Mapping to the renderer

- A node turns **red and pulses** when its state becomes "compromised" (after an IDS alert).
- The **SIEM / IDS** node turns amber once the first alert fires.
- Links the attacker has traversed turn red.

## Ideas to extend

- Write a Sigma or Suricata rule for each step and link it here.
- Replay real alerts from a home lab (for example `eve.json` from Suricata) instead of the scripted path.


#By HollyMully