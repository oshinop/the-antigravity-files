# Clearcut with telemetry switched off

Clearcut records sent with "Enable Telemetry" switched off, captured with `agy` 1.5.11. Content fields become `REDACTED`; IDs, client version, account tier and metadata keep flowing. Dotted numbers are protobuf field paths.

```text
POST play.googleapis.com/log
[…]
07:47:44.871 UTC — metadata record
  3.6.6.2 { 1: "installation_id", 2: "[installation ID]" }
  3.6.6.2 { 1: "machineId",       2: "" }
  […]

07:47:49.940 UTC — tool-step record
  3.6.17: "[client version]"
  3.6.22: "[account tier]"
  […]
  3.6.30.1:  "REDACTED"
  3.6.30.8:  "REDACTED"
  3.6.30.10: "[trajectory ID]"
  3.6.30.12: "REDACTED"
  […]
```
