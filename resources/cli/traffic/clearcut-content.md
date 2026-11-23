# Clearcut records carrying workspace content

Clearcut (`play.googleapis.com/log`) records from one `agy` 1.5.14 session with "Enable Telemetry" on: the user's prompt, a command and its output, the workspace's git root and branch, the git reflog, and a `.env` file the agent read. Times are the record's event time and the batch it went out in. Dotted numbers are protobuf field paths (`3.6` is the record's `JetskiTelemetryExtension`).

```text
POST play.googleapis.com/log

14:45:14.045 UTC (batch 14:45:16.505) - state_initialization_data
3.6.13.1.3.2.1.1 workspace_folder_absolute_uri: "file:///~/project"
3.6.13.1.3.2.1.2 git_root_absolute_uri:         "file:///~/project"
3.6.13.1.3.2.1.4 branch_name:                   "master"

14:45:32.119 UTC (batch 14:45:36.946) - tool-step record, user input
3.6.12.1.19.2 user_response: "Add a discount(total, pct) function to src/billing.py, then run git diff --stat."

14:48:44.815 UTC (batch 14:48:47.880) - tool-step record, run_command
3.6.22 user_tier_id: "[tier]"
3.6.12.1.5.4.2 tool_call name: "run_command"
3.6.12.1.5.4.3 tool_call args: "{\"BypassSandbox\":true,\"CommandLine\":\"git diff --stat\",\"WaitMsBeforeAsync\":5000,\"toolSummary\":\"Check git diff stat\"}"
3.6.12.1.28.23 command_line:  "git diff --stat"
3.6.12.1.28.6  exit_code:     0
3.6.12.1.28.21.1 combined_output.full:
  Created At: 2026-11-22T14:48:34Z
  Completed At: 2026-11-22T14:48:44Z
  Command exited with code 0.

  Command output:
   src/billing.py | 4 ++++
   1 file changed, 4 insertions(+)

14:49:36.539 UTC (batch 14:49:38.264) - tool-step record, read_file
3.6.12.1.14.1 absolute_path_uri: "file:///~/project/.git/logs/HEAD"
3.6.12.1.14.4 content:
  Created At: 2026-11-22T14:49:36Z
  Completed At: 2026-11-22T14:49:36Z
  Showing lines 1-1 of 1
  1: 0000000000000000000000000000000000000000 [commit ID] [git author] <[git email]> […]
3.6.12.1.14.12 num_bytes: 148

14:50:30.031 UTC (batch 14:50:33.516) - tool-step record, read_file
3.6.12.1.5.4.2 tool_call name: "read_file"
3.6.12.1.5.4.3 tool_call args: "{\"Path\":\"~/project/.env\"}"
3.6.12.1.14.1 absolute_path_uri: "file:///~/project/.env"
3.6.12.1.14.4 content:
  Created At: 2026-11-22T14:50:30Z
  Completed At: 2026-11-22T14:50:30Z
  Showing lines 1-1 of 1
  1: PAYMENT_API_KEY=[key]
3.6.12.1.14.11 num_lines: 1
3.6.12.1.14.12 num_bytes: 53
[…]
```
