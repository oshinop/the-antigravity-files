# Google's server sends 635 switches; `agy` reads 202 of them

`agy` 1.5.14 fetches a list of switches from Google every time it starts, then keeps polling for changes while it runs. Most of the list belongs to other products. The 202 switches `agy` does read control a lot:

- whether model reviews screen the agent's commands;
- whether auto mode exists, and whether new users start in it;
- which commands run without asking;
- whether remote control is on, and where its traffic goes;
- which system-prompt sections the model gets;
- which plugins are promoted or blocked.

Every switch, with its value and what it does, is in the [switch catalogue](../../resources/cli/server-switches/INDEX.md).

## Switches arrive at start and the cache polls every minute

After sign-in, the CLI's first calls are `fetchAdminControls`, `fetchAvailableModels` and `listExperiments`. Each sends only the project. The `listExperiments` response is 68 KB and holds 635 switches; `fetchAvailableModels` adds more switches for each model.

The language server keeps the switches in a cache whose poll loop runs on a 60-second ticker for as long as `agy` runs. In captured 1.5.14 sessions, the three calls went out again partway through, and `fetchAvailableModels` repeated about every five minutes.

Some switches take effect as soon as a new value arrives, with no restart. A listener re-reads `remote-control-setting-enabled` and `remote-control-proxy-server-url` and updates the remote-control connection while the CLI runs.

Evidence:
- Traffic: [listExperiments and fetchAvailableModels layouts](../../resources/cli/protocol/wire-account-and-config.md#listexperiments-68-kb); [the start-of-session sequence](../../resources/cli/protocol/protocol-and-requests.md#start-of-a-session)
- Binary: `jetski/language_server.CreateLanguageServerAndServe` passes 60 s to `jetski/experiments.NewMendelStateCache`; `jetski/experiments.(*MendelStateCache).pollLoop` polls on that ticker; `jetski/cli/store.(*Manager).refreshExperiments` copies refreshed values into the CLI's store; `jetski/language_server.registerMendelUpdateListener.func1` re-reads switches on each update
- Server: [experiments.json](../../resources/cli/server-switches/experiments.json), [available-models.json](../../resources/cli/server-switches/available-models.json)

## The server's value wins, and a model's own switches beat the global ones

`recomputeEffectiveFlagsLocked` builds the switch values separately for each model. It starts from the global `listExperiments` switches, lays that model's switches from `fetchAvailableModels` over them, and removes any names the model deletes. A lookup then returns, in order:

1. a local override, if there is one;
2. otherwise the merged server value;
3. otherwise false.

Local overrides come through the language server's `SetMendelFlagOverrides` call. It refuses with "Mendel flag overrides require developer mode" unless the server's `enable-developer-mode` is on, and that switch is off.

So for an ordinary sign-in, `agy` runs with whatever the server sends. Before the first response arrives, lookups wait. If no response comes, every switch reads false.

The binary also carries a snapshot of values for 131 switches, keyed by client, operating system, enterprise status and model. `agy` uses the snapshot instead of the server's values in two cases: when the Code Assist backend reports that the account has no access to its API (`HasNoCCPAAccess`), or when a startup option asks for the snapshot alone.

Evidence:
- Binary: `jetski/experiments.(*MendelStateCache).recomputeEffectiveFlagsLocked` merges global and per-model switches; `jetski/experiments.(*MendelAdapter).GetBool` checks local overrides, then server values, then returns false; `jetski/experiments.(*MendelStateCache).SetLocalOverrides` is reached from `jetski/language_server.(*Server).SetMendelFlagOverrides`; `jetski/language_server.(*snapshotFallbackFactory).NewProvider` chooses the snapshot (`jetski/experiments/snapshotmendelfp.newProviderForModel`) when `HasNoCCPAAccess` is true
- Prompt text: [the built-in snapshot](../../resources/cli/binary-1.5.14/configuration/generic-system-prompt-d040eb2.json)
- Server: [`enable-developer-mode`](../../resources/cli/server-switches/INDEX.md#enable-developer-mode)

## Two thirds of the list isn't for `agy`

| What `agy` 1.5.14 does with them | Switches |
|---|---|
| Code in the binary reads them | 202 |
| The built-in snapshot has a value, but no code reads them | 71 |
| Antigravity-style names the binary doesn't contain | 162 |
| Named `Product__name` after other Google products (`Chat__`, `DuetAi…`, `GeminiCLI…`, `Datacloud…`); none is in the binary | 200 |

Not all of the 202 reach the CLI:

- Several act only on the desktop app's Hub surface, or in language-server calls the CLI never makes.
- Three are read only by test code.
- Five sit in the name table that `populateExperimentsMap` builds, but no code or prompt template looks them up.

The catalogue marks each case.

Evidence:
- Server: [the switch catalogue](../../resources/cli/server-switches/INDEX.md), grouped by these four classes
- Binary: each switch name located in the 1.5.14 binary and traced to the functions that read it; `jetski/cortex/config.populateExperimentsMap` builds the name table

## What the server's values decide

### Safety reviews

The server switches on the reviews that screen the agent's tool calls. `policy-guardian-config` has no snapshot value. The server sends `{"allowed": true, "deployment": "ANTIGRAVITY", "enable_irreversible_vetting": true, "enable_reversibility_adjudication": true}`, which adds the `command_assessor` hook to the default agent and turns on the reversibility adjudicator that auto mode uses. `enable-command-assessor` is on, and `disable-command-assessor`, a remote off switch for it, is off.

The DeepMind monitor chain runs only where `allow-experimental-monitors` is true. The global value is false, but `fetchAvailableModels` sends true for 17 models, and the per-model value wins. `sonar-monitors-config` can replace the monitors' definitions and prompts; the server sends it empty, so the built-in definitions apply. How the reviews work is in [safety-reviews.md](safety-reviews.md).

Evidence:
- Binary: `jetski/cortex/config.PolicyGuardianEnabled`, `jetski/cortex/config.CommandAssessorEnabled`, `jetski/cortex/config.augmentConversationalConfigWithExperiment` (reads `allow-experimental-monitors` and `sonar-monitors-config`)
- Server: [`policy-guardian-config`](../../resources/cli/server-switches/INDEX.md#policy-guardian-config), [`enable-command-assessor`](../../resources/cli/server-switches/INDEX.md#enable-command-assessor), [`disable-command-assessor`](../../resources/cli/server-switches/INDEX.md#disable-command-assessor), [`allow-experimental-monitors`](../../resources/cli/server-switches/INDEX.md#allow-experimental-monitors), [`sonar-monitors-config`](../../resources/cli/server-switches/INDEX.md#sonar-monitors-config)

### Auto mode, permissions and the sandbox

Whether auto mode exists, whether new users start in it, which commands run without asking (`rm` among them) and whether the sandbox's network proxy lets everything through are all server switches; the full list with values is in [what the server can change](sandbox-and-permissions.md#what-the-server-can-change). Beyond that list, the code refuses auto mode for `gateway` sign-ins and leaves `adc` and `gcp` sign-ins to admin controls. `enable-trustedcli-config-files`, off, would activate trusted CLIs defined in local config files, which get their own sandbox, injected secrets and a way out of the sandbox.

Evidence:
- Binary: `jetski/cortex/core/components/config.buildDefaultAgent` adds the `auto_run_safety_gate` hook under `enable-auto-permission-preset`; `jetski/cortex/shared.TrustedCLI.activationGatesPass`
- Server: [`enable-auto-permission-preset`](../../resources/cli/server-switches/INDEX.md#enable-auto-permission-preset), [`permission-v2`](../../resources/cli/server-switches/INDEX.md#permission-v2), [`enable-trustedcli-config-files`](../../resources/cli/server-switches/INDEX.md#enable-trustedcli-config-files)

### Remote control

`remote-control-setting-enabled` is on. It makes `/remote-control` and `--remote-control` available unless an admin control disables them. `remote-control-proxy-server-url` names the relay, `jetski-webchannel.googleapis.com:443`. `agy` re-reads both while it runs, so the server can turn remote control on or off, or move its relay, in the middle of a session.

Evidence:
- Binary: `jetski/language_server.(*Server).getProxyServerURLWithState` reads the relay address; `jetski/language_server.registerMendelUpdateListener.func1` re-reads both switches on each update
- Server: [`remote-control-setting-enabled`](../../resources/cli/server-switches/INDEX.md#remote-control-setting-enabled), [`remote-control-proxy-server-url`](../../resources/cli/server-switches/INDEX.md#remote-control-proxy-server-url)

### Telemetry and diagnostics

`enforce-telemetry-disabled` can lock telemetry off and save that setting. It is off, so telemetry follows the user's setting. Three diagnostics switches are off as well:

- `enable-primes-tracing` would export startup traces;
- `enable-continuous-pprof` would capture CPU and heap profiles and upload them;
- `enable-conversation-memory-monitor` would report each conversation's memory use.

`log-artifacts-config` keeps the conversation transcript artifact on, with steps cut at 4,096 bytes and tool arguments at 2,048. What telemetry carries is in [data-collection.md](data-collection.md).

Evidence:
- Binary: `jetski/language_server.CreateLanguageServerAndServe` (reads `enforce-telemetry-disabled` and `enable-primes-tracing`), `jetski/language_server/profiling.(*Profiler).IsEnabled`, `jetski/language_server.(*Server).isConversationMemoryMonitorEnabled`, `jetski/cortex/config.LogArtifactsEnabled`
- Server: [`enforce-telemetry-disabled`](../../resources/cli/server-switches/INDEX.md#enforce-telemetry-disabled), [`enable-primes-tracing`](../../resources/cli/server-switches/INDEX.md#enable-primes-tracing), [`enable-continuous-pprof`](../../resources/cli/server-switches/INDEX.md#enable-continuous-pprof), [`enable-conversation-memory-monitor`](../../resources/cli/server-switches/INDEX.md#enable-conversation-memory-monitor), [`log-artifacts-config`](../../resources/cli/server-switches/INDEX.md#log-artifacts-config)

### Prompts and context

`generic-system-prompt` is on, so the templates use their general-purpose wording: "You are Antigravity, a powerful agentic AI assistant designed by the Google Deepmind team". Several switches add text to the model's context or replace it:

- `jetski-generative-ui-prompt` adds or removes the prompt section on interactive HTML widgets.
- `enable-url-artifacts` adds or removes the section on bookmarking URLs.
- `enable-work-efficiently-reminder` injects "Reminder: work efficiently. In this turn, issue in parallel every independent search, read and edit or write you need next." after any turn with a single tool call.
- The per-model `template__system_prompts__guidelines` replaces the `<guidelines>` section.
- `review-arm` and `deepagent-arm` can each replace a built-in subagent's whole definition, prompt included. The server sends both empty.

What the prompt contains is in [prompts-and-context.md](prompts-and-context.md).

Evidence:
- Binary: `jetski/prompt/template_provider/templateprovider.(*systemPromptData).ExperimentEnabled` lets prompt templates test switches; `jetski/cortex/core/components/config.buildDefaultAgent` adds the reminder hook and reads `review-arm` and `deepagent-arm`
- Server: [`generic-system-prompt`](../../resources/cli/server-switches/INDEX.md#generic-system-prompt), [`jetski-generative-ui-prompt`](../../resources/cli/server-switches/INDEX.md#jetski-generative-ui-prompt), [`enable-url-artifacts`](../../resources/cli/server-switches/INDEX.md#enable-url-artifacts), [`enable-work-efficiently-reminder`](../../resources/cli/server-switches/INDEX.md#enable-work-efficiently-reminder), [`review-arm`](../../resources/cli/server-switches/INDEX.md#review-arm), [`deepagent-arm`](../../resources/cli/server-switches/INDEX.md#deepagent-arm)

### Models, subagents and the swarm

`disable-teamwork-forced-flash-model` is false, so Teamwork runs on the Flash tier whichever model you picked. The server also decides:

- which subagents exist: `enable-review`, `enable-deepagent`, `enable-image-generator-subagent`, `enable-owl-slash-command`;
- whether `invoke_subagent` can spawn up to 10 workers at once: `convenient-multi-agent-coordination`, off;
- whether swarm members may use named channels: `native-swarm-channels-local`, off, so they share only `#main`.

Swarm and subagent behaviour is in [agents-and-swarm.md](agents-and-swarm.md).

Evidence:
- Binary: `jetski/cortex/config.TeamworkDefaultsToFlash`, `jetski/cortex/core/components/config.buildDefaultAgent` (adds each subagent under its switch), `jetski/cortex/core/native_swarm/nativeswarm.channelFlags.allow`
- Server: [`disable-teamwork-forced-flash-model`](../../resources/cli/server-switches/INDEX.md#disable-teamwork-forced-flash-model), [`enable-review`](../../resources/cli/server-switches/INDEX.md#enable-review), [`enable-deepagent`](../../resources/cli/server-switches/INDEX.md#enable-deepagent), [`convenient-multi-agent-coordination`](../../resources/cli/server-switches/INDEX.md#convenient-multi-agent-coordination), [`native-swarm-channels-local`](../../resources/cli/server-switches/INDEX.md#native-swarm-channels-local)

### Plugins and cloud features

`plugin-kill-switch` holds a list of plugins, with optional version patterns. `agy` refuses to install or upgrade a listed plugin and flags it if it's already installed. The server sends the list empty.

`agy-plugin-shelves` adds a "Google Workspace" shelf to the marketplace with the Docs, Sheets, Slides, Drive, Calendar and Gmail plugins.

The server also turns off several features the snapshot turns on, among them `enable-plugins`, `enable-mcp-apps`, `deprecate-workflows` and the webview browser subagent (`enable-browser-subagent-webview`).

`enable-remote-ai` decides whether conversations may run on Google's cloud backends instead of the local one. It is off.

Evidence:
- Binary: `jetski/language_server.(*Server).marketplaceIndex` reads `agy-plugin-shelves`; `jetski/cortex/config.BrowserSubagentWebviewEnabled`; `jetski/language_server.(*Server).remoteAIEnabled`
- Server: [`plugin-kill-switch`](../../resources/cli/server-switches/INDEX.md#plugin-kill-switch), [`agy-plugin-shelves`](../../resources/cli/server-switches/INDEX.md#agy-plugin-shelves), [`enable-plugins`](../../resources/cli/server-switches/INDEX.md#enable-plugins), [`enable-browser-subagent-webview`](../../resources/cli/server-switches/INDEX.md#enable-browser-subagent-webview), [`enable-remote-ai`](../../resources/cli/server-switches/INDEX.md#enable-remote-ai)
