# Analysis

Conclusions about the Antigravity CLI, `agy` 1.5.14, drawn from the [resources](../resources/README.md). Each finding lists its evidence underneath: functions in the 1.5.14 binary, named by their Go package path without the leading `google3/` and `third_party/`; captured traffic; the prompt text Google ships; the server's responses; and Google's own pages.

## Antigravity CLI

| Report | What it covers |
|---|---|
| [How `agy` 1.5.14 is built: one binary, a local server, and Google's backends](cli/architecture.md) | How `agy` is put together: the processes, the requests each one makes, what stays on disk |
| [Sign-in and plans: how `agy` 1.5.14 authenticates and what the account unlocks](cli/sign-in-and-plans.md) | OAuth sign-in, token storage, API-key mode, and what depends on the plan or account |
| [What leaves the machine: telemetry, trajectory uploads and identifiers](cli/data-collection.md) | What leaves the machine, where it goes, and what changes with telemetry off |
| [Google's models review the agent's tool calls before they run](cli/safety-reviews.md) | The models that review the agent's work: when they run, what they receive, whose quota they use |
| [The terminal sandbox covers commands only, and the agent can ask to leave it](cli/sandbox-and-permissions.md) | The terminal sandbox, the ways around it, permission modes and auto mode |
| [What `agy` sends the model: system prompt, tools, injected messages and compaction](cli/prompts-and-context.md) | How a model request is assembled: system prompt, tools, injected messages, compaction |
| [Helpers, swarms and timers: how `agy` runs more than one agent](cli/agents-and-swarm.md) | Subagents, the research helper, the swarm and scheduled work |
| [Google's server sends 635 switches; `agy` reads 202 of them](cli/server-switches.md) | What Google's server can change at start, and which of its switches `agy` actually reads |

## Antigravity desktop app 2.33.0

Static analysis of the macOS build.

| Document | Contents |
|---|---|
| [Overview](desktop/overview.md) | Architecture, sign-in, telemetry, findings and data inventory across the app and CLI |
| [Electron shell](desktop/electron-shell.md) | IPC, web views, the language-server launch, computer use, the browser agent, updates |
| [Language server](desktop/language-server.md) | The Go agent backend: endpoints, telemetry paths, tools, sandbox, local RPC |
