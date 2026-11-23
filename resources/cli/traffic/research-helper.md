# Research helper

A main agent launching the research helper and getting its report, captured with `agy` 1.5.14 (Gemini 4 Neon, Medium): the call and its result, the helper's tool configuration from a trajectory upload, the brief as the helper receives it, a refused write, and the report reaching the main agent while it was idle in headless mode (`agy -p`). The same session's report arriving during a later prompt is in [injected-messages.md](injected-messages.md). IDs and paths are replaced.

```text
--- Main agent: functionCall ---
invoke_subagent {
  "Role": "Codebase Researcher",
  "Prompt": "Explore the project in the workspace (~/project) to understand its structure, purpose, main components, and functionality. Return a concise summary of what the project does and its key files.",
  "TypeName": "research"
}

--- Main agent: functionResponse.output, two seconds later ---
Created At: 2026-11-22T14:20:52Z
Completed At: 2026-11-22T14:20:54Z
Created the following subagent:
{
  "conversationId": "[id]",
  "logAbsoluteUri": "file:///~/.gemini/antigravity-cli/brain/[id]/.system_generated/logs/transcript.jsonl",
  "workspaceUris": [
    "file:///~/project"
  ]
[…]

--- Main agent: next model reply (turn ends) ---
I've launched the research subagent to explore the project and will summarize its findings once it finishes.

--- Helper: labels and tools of its requests ---
labels.subagent_type: "research"
tools: read_file, search_web, run_command, send_message, schedule, manage_task
run_command parameters: CommandLine, IsDaemon, WaitMsBeforeAsync, toolSummary

--- Helper: run_command tool config (recordTrajectoryAnalytics upload, fragment) ---
"disableSandboxBypass":true, … "enableTerminalSandbox":true, … "readOnlySandbox":true, …
"schedule":{"enableDaemon":true,"enabled":true}

--- Helper: first user entry ---
The following is a <SYSTEM_MESSAGE> not actually sent by the user. It is provided by the system as important information to pay attention to.

<SYSTEM_MESSAGE>
[Message] timestamp=2026-11-22T14:20:54Z sender=[main agent conversation ID] priority=MESSAGE_PRIORITY_HIGH content=Explore the project in the workspace (~/project) to understand its structure, purpose, main components, and functionality. Return a concise summary of what the project does and its key files.
</SYSTEM_MESSAGE>

--- Helper asked to run write commands (another 1.5.14 session): its send_message ---
send_message { "Message": "Here are the results for the requested commands:\n\n(1) `echo […] > ~/project/[file] && cat […]`\nNot executed: as a read-only research subagent, I cannot run commands that write, create, or modify files or state.\n\n(2) `touch /tmp/[file] && echo […]`\nNot executed: as a read-only research subagent, I cann[…]" }

--- Helper in the same session: a network command ---
Command exited with code 6.

Command output:
curl: (6) Could not resolve host: example.com

--- Headless run: the main agent's history when the report arrives ---
user   <USER_REQUEST> Have the research subagent explore this project, then summarize what it does in two sentences. </USER_REQUEST> […]
model  functionCall invoke_subagent {…}
model  functionResponse invoke_subagent {…}
model  I've launched the research subagent to explore the project and will provide a two-sentence summary once it finishes.
user   The following is a <SYSTEM_MESSAGE> not actually sent by the user. […]
       <SYSTEM_MESSAGE>
       [Message] timestamp=2026-11-22T14:26:40Z sender=[helper conversation ID] priority=MESSAGE_PRIORITY_HIGH content=### Project Summary (`~/project`)
       […]
       </SYSTEM_MESSAGE>
```
