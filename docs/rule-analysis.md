# Custom Rule Analysis

This file explains what the custom signatures are intended to detect and what can safely be concluded from them.

## SID 1000001 — ICMP Echo Request Detected

```suricata
alert icmp any any -> $HOME_NET any (msg:"ICMP Echo Request Detected"; itype:8; sid:1000001; rev:2;)
```

- **Intent:** Alert on ICMP echo requests directed to HOME_NET.
- **Key option:** `itype:8` matches ICMP type 8 (echo request).
- **Validation status:** Freshly tested on 13 October 2026. Four pings generated four revision-2 alerts.
- **Limitation:** Echo requests are not inherently malicious. Context and frequency matter.

## SID 1000002 — SSH Connection Attempt

```suricata
alert tcp any any -> $HOME_NET 22 (msg:"SSH Connection Attempt"; flow:to_server; sid:1000002; rev:1;)
```

- **Intent:** Alert on matching TCP traffic directed to port 22 in the to-server direction.
- **Validation status:** Not freshly tested in the current session.
- **Limitation:** A matching packet does not prove an SSH login succeeded or failed.

## SID 1000003 — FTP Connection Attempt

```suricata
alert tcp any any -> $HOME_NET 21 (msg:"FTP Connection Attempt"; flow:to_server; sid:1000003; rev:1;)
```

- **Intent:** Alert on matching TCP traffic directed to port 21 in the to-server direction.
- **Validation status:** Not freshly tested in the current session.
- **Limitation:** This detects traffic matching the rule, not authenticated access or data transfer.

## SID 1000004 — TCP SYN threshold

```suricata
alert tcp any any -> $HOME_NET any (msg:"Possible Nmap SYN Scan Detected"; flags:S; flow:to_server; threshold:type both, track by_src, count 20, seconds 3; sid:1000004; rev:1;)
```

- **Intent:** Alert on a source producing at least the configured volume of matching SYN packets within the threshold window.
- **Validation status:** Not freshly tested in the current session.
- **Naming caveat:** The rule does not identify Nmap. A burst of SYN packets can have multiple explanations and is not proof of a port scan.
- **Suggested improvement:** Rename the message to something more neutral such as `Possible TCP SYN Burst`, then test and tune it against benign and controlled scan traffic in an isolated lab.

## SID 1000005 — ICMP echo-request threshold

```suricata
alert icmp any any -> $HOME_NET any (msg:"Possible ICMP Ping Sweep Detected"; itype:8; threshold:type both, track by_src, count 5, seconds 5; sid:1000005; rev:1;)
```

- **Intent:** Alert when a source generates a threshold volume of ICMP echo requests.
- **Validation status:** Not freshly tested in the current session.
- **Naming caveat:** The rule does not check for multiple distinct destination addresses. Repeated pings to one host can also match it, so the current signature is not conclusive evidence of a ping sweep.

## General interpretation

An IDS alert records traffic matching a signature. It should be investigated with relevant context and corroborating evidence. Do not report an alert alone as proof of compromise, attacker identity, successful authentication, or blocked traffic.
