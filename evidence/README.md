# Evidence

The initial evidence set contains a normalized sample of four verified revision-2 ICMP alerts from `fast.log`.

## Evidence already verified

- Suricata 8.0.5 configuration test succeeded.
- 389 rules loaded, with 0 failed and 0 skipped.
- Suricata service restarted and reported active/running.
- Four pings to `10.0.2.1` succeeded with 0% packet loss.
- Four matching `ICMP Echo Request Detected` alerts appeared in `fast.log`.

See [alert-sample.json](alert-sample.json). Its entries are normalized from the visible `fast.log` output, not copied from raw EVE JSON. The file is intended to make the observed fields easy to review.

## Screenshots to capture from the real lab

Add screenshots when available; do not create mock screenshots or claim that absent images are evidence.

1. **Configuration validation:** terminal showing the successful `suricata -T` result and the 389/0/0 rule counts.
2. **Service status:** terminal showing `systemctl status suricata` with `active (running)`.
3. **Successful ping:** terminal showing four transmitted, four received, and 0% packet loss.
4. **Detection output:** terminal showing the four revision-2 `ICMP Echo Request Detected` entries in `fast.log`.

Suggested filenames: `config-test.png`, `service-status.png`, `ping-test.png`, and `icmp-alerts.png`.

Before sharing screenshots, review them for personal paths, usernames, tokens, unrelated terminal output, or other information you do not want public.
