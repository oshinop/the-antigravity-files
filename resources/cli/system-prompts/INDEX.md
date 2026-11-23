# System prompts

The system prompt each caller sent in `agy` 1.5.14 requests.

| File | Caller | Bytes | Sections |
|---|---|---|---|
| [main-agent.txt](main-agent.txt) | Main agent (Gemini 4 Neon) | 18,967 | `identity`, `terminal_sandbox`, `artifacts`, `conversation_transcript`, `guidelines`, `work_efficiently`, `communication_style`, `slash_commands`, `messaging`, `subagents`, `skills`, `user_information` |
| [main-agent-gemini-4-hydrogen.txt](main-agent-gemini-4-hydrogen.txt) | Main agent on Gemini 4 Hydrogen, with three extra sections | 22,262 | `identity`, `terminal_sandbox`, `artifacts`, `conversation_transcript`, `guidelines`, `efficient_code_reading`, `code_quality`, `final_answer_accuracy`, `work_efficiently`, `communication_style`, `slash_commands`, `messaging`, `subagents`, `skills`, `user_information` |
| [swarm-member.txt](swarm-member.txt) | A member of a `/swarm` | 26,002 | `identity`, `terminal_sandbox`, `artifacts`, `conversation_transcript`, `guidelines`, `work_efficiently`, `communication_style`, `slash_commands`, `messaging`, `subagents`, `skills`, `user_information`, `swarm_worker` |
| [research-subagent.txt](research-subagent.txt) | The read-only research helper | 6,503 | `Identity`, `user_information`, `subagent_reminder`, `messaging`, `artifacts` |
| [command-adjudicator.txt](command-adjudicator.txt) | Auto mode's reviewer for commands leaving the sandbox | 10,891 | — |
| [title-generator.txt](title-generator.txt) | Conversation title generator | 626 | — |
