# wazuh-watchguard-decoders

Custom Wazuh decoders for WatchGuard Firebox syslog. Covers admin and
VPN authentication, config changes (alias/policy), traffic, and
blocked-site events.

## Decoders
| Decoder | Event |
|---|---|
| watchguard-mgmt-login | Management login success |
| watchguard-wsm-login-fail | WSM login rejected |
| watchguard-vpn-login | Mobile VPN login |
| watchguard-ikev2-login / -fail | IKEv2 auth accepted/rejected |
| watchguard-alias-* / policy-* | Alias/Policy added, deleted, modified |
| watchguard-traffic-0148 / 0151 | Traffic logs with GeoIP |
| watchguard-blocked-site | Blocked site (msg_id 3000-0173) |

## Install
1. Copy `decoders/watchguard_decoders.xml` to `/var/ossec/etc/decoders/`
2. `systemctl restart wazuh-manager`

## Rules
Copy `rules/watchguard_rules.xml` to `/var/ossec/etc/rules/` and restart
`wazuh-manager`.

| Rule ID | Level | Detects | MITRE |
|---|---|---|---|
| 114502 | 12 | Management login success | |
| 114503 | 12 | Mobile VPN login success | |
| 114504 | 12 | WSM login failure | |
| 114505 | 12 | IKEv2 authentication failure | |
| 114506 / 114507 | 0 | Traffic logs (parsed, no alert) | |
| 114517 | 8 | Blocked site access | T1071 |
| 114523 | 12 | Alias added | |

Verified with `wazuh-logtest`: 114502, 114504, 114505, 114506, 114517,
114523. Not yet verified: 114503, 114507.

## Known gaps
- No rule for successful IKEv2 login (decoder exists, no alert fires).
- No rules yet for policy/alias deleted or modified events.
- No MITRE mapping on auth rules yet.

## Test
Paste a line from `samples/sample.log` into:
`/var/ossec/bin/wazuh-logtest`

## Lessons learned
- A decoder can match via `prematch` but still extract nothing if the
  regex is too specific. `wazuh-logtest` showed the IKEv2 fail decoder
  matching "Authentication of Admin user" logs with zero fields
  extracted and a blank alert description. Lesson: always check Phase 2
  fields, not just the decoder name.

## Note
All sample data is synthetic (RFC 5737 documentation IPs).
