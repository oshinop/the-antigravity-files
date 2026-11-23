# Traffic excerpts

Excerpts of requests `agy` sent and responses it received. Each says which version captured it.

| Excerpt | What it is |
|---|---|
| [Clearcut before sign-in (1.5.14)](clearcut-before-sign-in.md) | The first run of `agy` 1.5.14 sent three Clearcut batches before the user signed in: at 12:11:30.735, 12:12:25.638 and 12:12:25.820 UTC. |
| [Clearcut records carrying workspace content](clearcut-content.md) | Clearcut (`play.googleapis.com/log`) records from one `agy` 1.5.14 session with "Enable Telemetry" on: the user's prompt, a command and its output, the workspace's git root and branch, the git reflog, and a `.env` file the agent read. |
| [Clearcut file-read record with telemetry switched off](clearcut-optout-file-read.md) | The Clearcut record for the agent reading a one-line `.env` file, sent with "Enable Telemetry" switched off (captured with `agy` 1.5.11, batch at 07:48:45.628 UTC). |
| [Clearcut with telemetry switched off](clearcut-optout.md) | Clearcut records sent with "Enable Telemetry" switched off, captured with `agy` 1.5.11. |
| [Clearcut tool-step record](clearcut-tool-step.md) | A Clearcut (`play.googleapis.com/log`) record for one tool step, captured with `agy` 1.5.11. |
| [Compaction](compaction.md) | One compaction on Gemini 4 Argon with `AGY_CLI_AUTO_COMPACT_OVERRIDE=60000`, captured with `agy` 1.5.14: the request sequence around it, the summary request, the message that replaced the history, and the checkpoint limit as it appears in trajectory uploads with and without the override. |
| [Endpoints contacted in a 1.5.14 session](endpoints-1514.md) | Every HTTP destination contacted in four minutes of an interactive `agy` 1.5.14 session (two prompts, one of them delegated to a research helper), with "Enable Telemetry" on. |
| [Endpoints contacted in a session](endpoints.md) | Every HTTP destination contacted in one interactive session, captured with `agy` 1.5.11 and grouped by host and path. |
| [Injected messages](injected-messages.md) | Text the client adds to the conversation of a main agent or swarm member, from `agy` 1.5.14 model requests. |
| [Injected context](injections.md) | How the client wraps text that other models wrote (helper briefs, swarm posts, timer ticks) before it reaches the main agent, captured with `agy` 1.5.11, plus the `<EPHEMERAL_MESSAGE>` wrapper from the binary. |
| [Review-model request](monitor-request.md) | An escalated review by Gemini 4 Hydrogen Sonar of a single file-listing command, captured with `agy` 1.5.11. |
| [Research helper](research-helper.md) | A main agent launching the research helper and getting its report, captured with `agy` 1.5.14 (Gemini 4 Neon, Medium): the call and its result, the helper's tool configuration from a trajectory upload, the brief as the helper receives it, a refused write, and the report reaching the main agent while it was idle in headless mode (`agy -p`). |
| [Review verdicts: step review, escalation, reversibility check](review-verdicts.md) | Records from `agy` 1.5.14 sessions on Gemini 4 Neon (Medium) in auto mode. |
| [A cron that should never fire](schedule-cron.md) | A `schedule` call with `0 0 31 2 *` (midnight on 31 February, a date that never occurs), captured with `agy` 1.5.14. |
| [A session started with `--dangerously-skip-permissions`](skip-permissions.md) | Records from one `agy` 1.5.14 session launched with `--dangerously-skip-permissions` on Gemini 4 Neon (Medium). |
| [Swarm](swarm.md) | A small `/swarm` captured with `agy` 1.5.14 (every member on Gemini 4 Neon, High): the coordinator's calls in its first turn, what `add` returns, a member's first two entries, the monitor's timer and a wait that ended in a tick. |
| [Trajectory upload of a research helper (1.5.14)](trajectory-upload.md) | A `recordTrajectoryAnalytics` upload from `agy` 1.5.14 at 14:52:08.827 UTC (406,449 bytes): the research helper's whole trajectory, 80 steps, sent when the helper finished. |
| [Trajectory upload](trajectory.md) | A `recordTrajectoryAnalytics` upload (216,046 bytes decoded), captured with `agy` 1.5.11. |
