# Clearcut before sign-in (1.5.14)

The first run of `agy` 1.5.14 sent three Clearcut batches before the user signed in: at 12:11:30.735, 12:12:25.638 and 12:12:25.820 UTC. The OAuth token exchange came at 12:17:18. The records carry no account tier, are marked `is_redacted`, and include the installation ID and a description of the terminal. Dotted numbers are protobuf field paths; `3.6.6.2` entries are the analytics event's key-value pairs.

```text
POST play.googleapis.com/log                       12:11:30.735 UTC, 14 records

3.6.16 ide_name:     "antigravity"
3.6.17 ide_version:  "1.5.14"
3.6.22 user_tier_id: ""
3.6.24 is_redacted:  1
3.6.27 product_name: "cli"

3.6.6.1 event_type: "LS_BINARY_STARTUP" | "startup_completed" | "hub_store_load_succeeded"
                    | "terminal_env" | "env_overrides" | "model_config_overrides" | […]

event "hub_store_load_succeeded"
3.6.6.2 { 1: "installation_id",                 2: "[installation ID]" }
3.6.6.2 { 1: "antigravity_ide_installation_id", 2: "" }
3.6.6.2 { 1: "machineId",                       2: "" }

event "terminal_env"
3.6.6.2 { 1: "shell",            2: "bash" }
3.6.6.2 { 1: "term",             2: "[terminal]" }
3.6.6.2 { 1: "multiplexer",      2: "[multiplexer]" }
3.6.6.2 { 1: "color_depth",      2: "256color" }
3.6.6.2 { 1: "rows_bucket",      2: "40_59" }
3.6.6.2 { 1: "cols_bucket",      2: "200_plus" }
3.6.6.2 { 1: "is_ssh",           2: "false" }
3.6.6.2 { 1: "is_wsl",           2: "false" }
3.6.6.2 { 1: "is_cloud_shell",   2: "false" }
3.6.6.2 { 1: "executionMode",    2: "interactive" }
[…]

event "env_overrides"
3.6.6.2 { 1: "AGY_CLI_DISABLE_AUTO_UPDATE", 2: "on" }
[…]

POST play.googleapis.com/log                       12:12:25.638 UTC, log source 327
3.6.3.4 "LS_ERROR"
3.6.3.5 "antigravity-cli"
3.6.3.6 "Failed to poll ListExperiments: error getting token source: You are not logged into Antigravity."
3.6.18.2.3 "[32 hex]"
3.6.18.2.5 "[installation ID]"
```
