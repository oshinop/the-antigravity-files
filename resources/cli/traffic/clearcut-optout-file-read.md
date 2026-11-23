# Clearcut file-read record with telemetry switched off

The Clearcut record for the agent reading a one-line `.env` file, sent with "Enable Telemetry" switched off (captured with `agy` 1.5.11, batch at 07:48:45.628 UTC). The path and contents are gone; the tool name, the file's size, the IDs and the product metadata remain. Compare the same step with telemetry on in [clearcut-content.md](clearcut-content.md). Dotted numbers are protobuf field paths.

```text
POST play.googleapis.com/log
[…]
3.6.22 user_tier_id: "[tier]"
3.6.24 is_redacted:  1
3.6.27 product_name: "cli"
3.6.12.1.5.4.1 tool_call id:   "[id]"
3.6.12.1.5.4.2 tool_call name: "read_file"
3.6.12.1.5.20.1 trajectory_id: "[trajectory ID]"
3.6.12.1.14.11 num_lines:        1
3.6.12.1.14.12 num_bytes:        53
3.6.12.1.14.20 line_range_bytes: 52
3.6.12.8 root_conversation_id: "[conversation ID]"
[…]
```
