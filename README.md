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
