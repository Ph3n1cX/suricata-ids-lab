# Suricata IDS Lab

A small home lab built with **Suricata 8.0.5** on Kali Linux to practice network intrusion detection, custom rule tuning, configuration validation, and alert verification.

> **Project status:** Initial ICMP detection test verified. This is a learning lab, not a production SOC deployment.

## Objectives

- Configure Suricata to monitor the `eth0` interface using AF_PACKET.
- Validate the configuration and loaded rules.
- Write and tune a custom ICMP detection rule.
- Generate benign test traffic and verify the resulting alert in `fast.log`.
- Document findings, limitations, and next steps as a SOC-style investigation.

## Lab environment

| Item | Observed value |
|---|---|
| Operating system | Kali Linux |
| Suricata version | 8.0.5 RELEASE |
| Capture method | AF_PACKET |
| Interface | `eth0` |
| Sensor IP | `10.0.2.7` |
| Test destination | `10.0.2.1` (VirtualBox NAT gateway) |
| HOME_NET | `10.0.2.0/24` |
| Rule files reported by config | `suricata.rules`, `local.rules` |

## Verified results

1. `sudo suricata -T -c /etc/suricata/suricata.yaml -v` completed successfully.
2. Suricata reported **389 rules loaded, 0 failed, 0 skipped**.
3. The service restarted successfully and was active.
4. A test using `ping -c 4 10.0.2.1` received four replies with **0% packet loss**.
5. The initial rule (SID `1000001`, revision 1) alerted on both ICMP echo requests and echo replies.
6. The rule was tuned to match ICMP type 8 (echo request), with message `ICMP Echo Request Detected` and revision 2.
7. After restarting Suricata, four new request alerts appeared in `fast.log`; the reply traffic did not trigger this revised rule.

See [the investigation report](docs/investigation-report.md), [rule analysis](docs/rule-analysis.md), and [evidence notes](evidence/README.md).

## Custom rule

The tuned rule is:

```suricata
alert icmp any any -> $HOME_NET any (msg:"ICMP Echo Request Detected"; itype:8; sid:1000001; rev:2;)
```

The `itype:8` option restricts this signature to ICMP echo requests. Suricata's rule documentation describes `itype` as a way to match a specific ICMP message type.

## Reproduce the test

On a lab machine where Suricata is installed and configured similarly:

```bash
sudo suricata -T -c /etc/suricata/suricata.yaml -v
sudo systemctl restart suricata
ping -c 4 10.0.2.1
sudo tail -n 20 /var/log/suricata/fast.log
```

The destination is specific to this VirtualBox NAT lab. Use the correct reachable address for your own isolated lab if it differs. Only generate traffic on systems and networks you are authorized to test.

## Repository structure

- `rules/local.rules` — custom rule examples from the lab, including the verified tuned ICMP rule.
- `docs/investigation-report.md` — evidence, analysis, and conclusion.
- `docs/rule-analysis.md` — purpose and limitations of each custom rule.
- `evidence/alert-sample.json` — normalized sample of the four verified request alerts.
- `evidence/README.md` — evidence provenance and recommended screenshots.

## Current limitations and next steps

- Only the ICMP echo-request rule has been freshly tested and verified in the current session.
- The SSH, FTP, TCP SYN threshold, and ICMP threshold rules are documented as configured, but have **not** been freshly validated as part of this test.
- A TCP SYN burst is not proof that Nmap was used or that a scan succeeded.
- The current ICMP threshold rule does not establish that multiple distinct destinations were probed, so it does not conclusively detect a ping sweep.
- Alerts indicate matching traffic, not compromise. This lab is configured in IDS mode and the tested alert action was not evidence of traffic blocking.
- Screenshots should be added from the actual lab session; none are fabricated or included as if already captured.

## References

- [Official Suricata rule introduction](https://docs.suricata.io/en/latest/rules/intro.html)
- [Official Suricata ICMP rule keywords](https://docs.suricata.io/en/latest/rules/header-keywords.html#icmp-keywords)
- [Official Suricata EVE JSON output documentation](https://docs.suricata.io/en/latest/output/eve/eve-json-output.html)
