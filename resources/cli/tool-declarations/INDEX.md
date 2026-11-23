# Tool declarations

The tools each caller was given in `agy` 1.5.14 requests, as sent.

## Main agent: [main-agent.json](main-agent.json)

| Tool | Description begins |
|---|---|
| `read_file` | Read the contents of a text, image, pdf, video, or audio file. Each read returns at most 800 lines or 46080 bytes. |
| `run_command` | Execute a command in the workspace. Operating System: [os]. Shell: [shell]. Each command runs in a new session (working di… |
| `manage_task` | Manage the background tasks of this conversation: 'list' the running tasks, check a task's 'status' and recent log outpu… |
| `send_message` | Send a message to another agent, such as a subagent or a peer. Not a channel to the user. |
| `schedule` | Schedule a one-shot timer (DurationSeconds) or a recurring cron job (CronExpression) that notifies you via Prompt. This … |
| `invoke_subagent` | Launch a subagent. It runs in the background with its own prompt and sends you a message when it completes or needs guid… |
| `define_subagent` | Define a new subagent type that can then be invoked repeatedly. Subagents always have tools to research the codebase and… |
| `manage_subagents` | Manage the direct subagents of this conversation. 'list' reports each one's conversationId (the id used to kill or messa… |
| `write_file` | Create a new file with the given contents, or append to an existing file when Append is true. Parent directories are cre… |
| `edit_file` | Use this tool to edit an existing file. Follow these rules: |
| `search_web` | Performs a web search for a given query. Returns a summary of relevant information along with URL citations. |
| `run_workflow` | Run a Python workflow script that orchestrates many agents and tool calls. Use it when the orchestration itself is code:… |
| `swarm` | Manage the swarm of agents working with you on the shared message board. ONLY call this tool when the user explicitly in… |
| `ask_question` | Ask the user one or more multiple-choice questions, e.g. to clarify requirements, and wait for the answers. Don't use it… |

## A member of a `/swarm`: [swarm-member.json](swarm-member.json)

| Tool | Description begins |
|---|---|
| `read_file` | Read the contents of a text, image, pdf, video, or audio file. Each read returns at most 800 lines or 46080 bytes. |
| `run_command` | Execute a command in the workspace. Operating System: [os]. Shell: [shell]. Each command runs in a new session (working di… |
| `manage_task` | Manage the background tasks of this conversation: 'list' the running tasks, check a task's 'status' and recent log outpu… |
| `schedule` | Schedule a one-shot timer (DurationSeconds) or a recurring cron job (CronExpression) that notifies you via Prompt. This … |
| `write_file` | Create a new file with the given contents, or append to an existing file when Append is true. Parent directories are cre… |
| `edit_file` | Use this tool to edit an existing file. Follow these rules: |
| `search_web` | Performs a web search for a given query. Returns a summary of relevant information along with URL citations. |
| `run_workflow` | Run a Python workflow script that orchestrates many agents and tool calls. Use it when the orchestration itself is code:… |
| `swarm` | Manage the swarm of agents working with you on the shared message board. ONLY call this tool when the user explicitly in… |
| `post_message` | Post a message to the swarm message board, where every agent can read it. Messages are tagged with your agent id. |
| `read_board` | Read messages from the swarm message board, oldest first. Each message carries a sequence number; pass the last one you … |
| `swarm_wait` | Sleep until another member of the swarm posts to the board. Waiting costs nothing: you consume no tokens while waiting, … |
| `ask_question` | Ask the user one or more multiple-choice questions, e.g. to clarify requirements, and wait for the answers. Don't use it… |

## The research helper: [research-subagent.json](research-subagent.json)

| Tool | Description begins |
|---|---|
| `read_file` | Read the contents of a text, image, pdf, video, or audio file. Each read returns at most 800 lines or 46080 bytes. |
| `search_web` | Performs a web search for a given query. Returns a summary of relevant information along with URL citations. |
| `run_command` | Execute a command in the workspace. Operating System: [os]. Shell: [shell]. Each command runs in a new session (working di… |
| `send_message` | Send a message to another agent, such as a subagent or a peer. Not a channel to the user. |
| `schedule` | Schedule a one-shot timer (DurationSeconds) or a recurring cron job (CronExpression) that notifies you via Prompt. This … |
| `manage_task` | Manage the background tasks of this conversation: 'list' the running tasks, check a task's 'status' and recent log outpu… |

## The command adjudicator: [command-adjudicator.json](command-adjudicator.json)

| Tool | Description begins |
|---|---|
| `read_file` | Read the contents of a text, image, pdf, video, or audio file. Each read returns at most 800 lines or 46080 bytes. |
| `run_command` | Execute a command in the workspace. Operating System: [os]. Shell: [shell]. Each command runs in a new session (working di… |
| `list_directory` | Lists directory entries and inspects files or executables (existence, text/script vs binary type, size, modification tim… |
| `search_files` | Searches files or directories in memory for lines matching a case-insensitive regular expression without spawning a shel… |
| `search_transcript` | Searches the coding agent's session transcript in memory without spawning a shell. Matches case-insensitive literal subs… |
| `submit_verdict` | Records your final decision about the command. Call this exactly once, as the last thing you do. Set reversible to true … |
