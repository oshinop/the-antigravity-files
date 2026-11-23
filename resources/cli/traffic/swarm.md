# Swarm

A small `/swarm` captured with `agy` 1.5.14 (every member on Gemini 4 Neon, High): the coordinator's calls in its first turn, what `add` returns, a member's first two entries, the monitor's timer and a wait that ended in a tick. Conversation IDs and paths are replaced.

```text
--- Coordinator (the main agent): calls in its first turn ---
read_file    { "Path": "~/.gemini/antigravity-cli/builtin/skills/swarm_pane/SKILL.md" }
run_command  { "toolSummary": "Inspect workspace layout", "CommandLine": "ls -la ~/project; […]", "WaitMsBeforeAsync": 5000 }
swarm        { "action": "title", "title": "Add vendor docstring and json export tests" }
swarm        { "action": "add", "name": "docstrings-test", "brief": "You own ~/project/tests/test_docstrings.sh. Do not edit vendor/ or any other files. […]" }
swarm        { "action": "add", "name": "json-names-test", "brief": "You own ~/project/tests/test_json_names.sh. […]" }
swarm        { "action": "add", "name": "verifier", "brief": "You are the verifier for two new test scripts: […]" }
swarm        { "action": "add", "name": "monitor", "brief": "You are this swarm's stall monitor. Load the swarm_monitor skill and follow it. Post only to break a stall." }
write_file   { "Path": "~/.gemini/antigravity-cli/brain/[id]/.system_generated/swarm/plan.json", "Content": "{\n  \"goal\": {\n    \"title\": \"Add vendor docstring and json export tests\", […]" }

--- Coordinator: output of the last add ---
{"status":"added","name":"monitor","model":"gemini-4-neon-high","model_display_name":"Gemini 4 Neon (High)","conversation_id":"[id]",
 "open_board":"Run /swarm board (default shortcut alt+s) to open the live board.",
 "note":"The brief is on the board as #4, where every member of the swarm can read it. post_message, read_board and swarm_wait are not available until your next turn. Add the rest of the workers, then end your turn: a post that names @coordinator wakes you, so brief each worker to post to @coordinator when it is done. When you reply to the user, end with open_board, exactly as given, on its own line, so they can open the live board."}

--- Member json-names-test: first two user entries ---
<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T13:12:15Z sender=[id] priority=MESSAGE_PRIORITY_HIGH content=[swarm] new board message #2
</SYSTEM_MESSAGE>

<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T13:12:15Z sender=[coordinator conversation ID] priority=MESSAGE_PRIORITY_HIGH content=You have just started. Your assignment (recorded on the board as #2 for @json-names-test):

You own ~/project/tests/test_json_names.sh. Do not edit vendor/ or any other files.
[…]
5. Otherwise, when passing, post: @verifier [done] <concise summary of script and test results>
</SYSTEM_MESSAGE>
(each entry is preceded by the usual "The following is a <SYSTEM_MESSAGE> not actually sent by the user. […]" line)

--- Monitor: its timer ---
schedule { "IsDaemon": true, "Prompt": "[monitor] tick: check the swarm for a stall", "CronExpression": "*/3 * * * *" }
→ Tool is running as a background task with task id: [id]/task-6

--- Monitor: a swarm_wait woken by the first tick ---
Created At: 2026-11-22T13:12:52Z
Completed At: 2026-11-22T13:12:52Z
Waiting for a message that calls on you by name, or on @all posted after #4.

The following is a <SYSTEM_MESSAGE> not actually sent by the user. It is provided by the system as important information to pay attention to.

<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T13:13:10Z sender=[id]/task-6 priority=MESSAGE_PRIORITY_HIGH content=[monitor] tick: check the swarm for a stall (iteration 1)
</SYSTEM_MESSAGE>
```
