# Analysis

Raw records of what `agy` sent and received, and the findings drawn from the binaries.

## Antigravity CLI

| Document | Contents |
|---|---|
| [One session, traced](cli/runs/session-1/README.md) | Timeline of a two-prompt session: the helper outliving the turn, review models checking tool calls, telemetry throughout |
| [Clearcut tool-step record](cli/traffic/clearcut-tool-step.md) | A Clearcut (`play.googleapis.com/log`) record for one tool step. |
| [Clearcut with telemetry switched off](cli/traffic/clearcut-optout.md) | Clearcut records sent with "Enable Telemetry" switched off. |
| [Trajectory upload](cli/traffic/trajectory.md) | A `recordTrajectoryAnalytics` upload (216,046 bytes decoded). |
| [Review-model request](cli/traffic/monitor-request.md) | An escalated review by Gemini 4 Hydrogen Sonar of a single file-listing command. |
| [Injected context](cli/traffic/injections.md) | How the client wraps text that other models wrote (helper briefs, swarm posts, timer ticks) before it reaches the main agent, plus the `<EPHEMERAL_MESSAGE>` wrapper from the binary.. |
| [Endpoints contacted in a session](cli/traffic/endpoints.md) | Every HTTP destination contacted in one interactive session, grouped by host and path. |
| [Static analysis of `agy` 1.5.11](cli/static-analysis-1.5.11.md) | Sign-in, token storage, endpoints, telemetry, sandbox and commands, from the binary |

## Antigravity desktop app 2.33.0

Static analysis of the macOS build; the app was not signed in to.

| Document | Contents |
|---|---|
| [Overview](desktop/overview.md) | Architecture, sign-in, telemetry, findings and data inventory across the app and CLI |
| [Electron shell](desktop/electron-shell.md) | IPC, web views, the language-server launch, computer use, the browser agent, updates |
| [Language server](desktop/language-server.md) | The Go agent backend: endpoints, telemetry paths, tools, sandbox, local RPC |
