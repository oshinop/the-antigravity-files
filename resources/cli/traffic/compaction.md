# Compaction

One compaction on Gemini 4 Argon with `AGY_CLI_AUTO_COMPACT_OVERRIDE=60000`, captured with `agy` 1.5.14: the request sequence around it, the summary request, the message that replaced the history, and the checkpoint limit as it appears in trajectory uploads with and without the override. The request layout is in [wire-side-model-requests.md](../protocol/wire-side-model-requests.md#compaction); what the limits mean is in [prompts-and-context.md](../../../analysis/cli/prompts-and-context.md#compaction-replaces-the-history-with-a-summary-the-same-model-writes).

```text
--- Main-model requests (requestType, model, entries in contents, promptTokenCount) ---
13:21:41  agent       gemini-4-argon-medium   1   9,413   first prompt
          [six read_file results come back in one step]
13:22:31  checkpoint  gemini-4-argon-medium   4  86,073   history + summary prompt; 588 output tokens
13:24:37  agent       gemini-4-argon-medium   1  10,043   <CONTEXT_SUMMARY> only
13:24:58  agent       gemini-4-argon-medium   3  10,884   next prompt
13:25:20  checkpoint  gemini-4-argon-medium   6  86,843   history + summary prompt; 680 output tokens
13:26:06  agent       gemini-4-argon-medium   1  10,184   <CONTEXT_SUMMARY> only

--- Last entry of the checkpoint request (3,937 bytes; identical to compaction/default.txt) ---
You have been working on the task described above but have not yet completed
it. Write a continuation summary that will allow you (or another instance of
yourself) to resume work efficiently in a future context window where the
full conversation history will NOT be available-only this summary.
[…]
Wrap your response in <summary></summary> tags.

The checkpoint request also carries the main agent's system prompt (18,961 chars) and all 14 tool declarations,
with generationConfig { maxOutputTokens: 32768, thinkingLevel: MEDIUM }.

--- The only entry of the next agent request (2,443 chars) ---
<CONTEXT_SUMMARY>
The following is a summary of the conversation history that has been truncated to fit within the context window:

This summary was generated at 2026-11-22T13:22:31Z.

# User Requests
The following were the most recent user requests in chronological order:
1. [the prompt]

# Previous Session Summary:
<summary>
1. **Task Overview**
[…]
7. **Commitments & Constraints**
[…]
</summary>

# Conversation Logs
Start with the compact transcript; if a step has `truncated_fields`, read only that specific line in `transcript_full.jsonl`:
- `<appDataDir>/brain/<conversation-id>/.system_generated/logs/transcript.jsonl`
- `<appDataDir>/brain/<conversation-id>/.system_generated/logs/transcript_full.jsonl`
</CONTEXT_SUMMARY>

--- Checkpoint config in recordTrajectoryAnalytics uploads (fragment) ---
Gemini 4 Argon, AGY_CLI_AUTO_COMPACT_OVERRIDE=60000:
  …"maxOutputTokens":32768,"maxOverheadRatio":0.15,"maxSubagentSnapshots":20,"maxTokenLimit":60000,"maxUserRequestBytes":1024,"maxUserRequestMediaTokens":1200,"maxUserRequests":10,…
Gemini 4 Argon, AGY_CLI_AUTO_COMPACT_OVERRIDE=10%:
  …"maxSubagentSnapshots":20,"maxTokenLimit":104857,"maxUserRequestBytes":1024,…
Gemini 4 Neon, AGY_CLI_AUTO_COMPACT_OVERRIDE=60000 (ignored; prompts reached 161,445 tokens with no compaction):
  …"maxSubagentSnapshots":20,"maxTokenLimit":512000,"maxUserRequestBytes":1024,…
```
