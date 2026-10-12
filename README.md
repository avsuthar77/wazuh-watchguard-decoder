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
| watchguard-ikev2-login / -fail | IKEv2/Admin auth accepted/rejected |
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
- A decoder can match via `prematch` but still capture nothing if the
  regex is too specific. My IKEv2 fail decoder had "IKEv2 user"
  hardcoded and silently missed "Admin user" logs.
- Test with `wazuh-logtest` after every change, not only the prematch.
- Remove dead decoders: I deleted a legacy "Blocked site" decoder after
  `grep` showed the firewall no longer emits that format.

## Note
All sample data is synthetic (RFC 5737 documentation IPs).
