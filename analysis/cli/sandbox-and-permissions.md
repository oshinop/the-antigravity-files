# The terminal sandbox covers commands only, and the agent can ask to leave it

`agy` 1.5.14 runs the agent's shell commands in an OS-level sandbox that hides most of the file system and cuts off the network. It covers `run_command` and nothing else. The agent can ask to run a command outside it; in auto mode a Gemini model, not you, approves most of those requests. `--dangerously-skip-permissions` removes the sandbox entirely while the status bar still reads `auto`. Several of the switches that decide whether auto mode is offered, which commands skip review and whether the adjudicator runs come from Google's server.

## The sandbox mounts the workspace and blocks the rest

The sandbox is Google's `exebox` package: a file tree that denies by default and mounts allowed paths, and a local network proxy with its own certificate. The proxy refuses requests by rule ("sandbox proxy refused HTTP %s (rule: %s)") and can write audit logs ("Path where structured sandbox proxy audit logs (JSONL) will be emitted; disabled if empty"). Google's docs name the operating-system mechanism underneath: Seatbelt (`sandbox-exec`) on macOS and AppContainer on Windows.

What a main-agent command inside the sandbox sees:

| Path | Access |
|---|---|
| Workspace | read-write |
| Workspace `.git` | read-only |
| `/tmp` | read-write |
| The conversation's own `brain/<conversation>` folder | read-write |
| `~/.gemini/antigravity-cli/{bin,builtin}` | read-only |
| `~/.config`, `~/.gemini/config`, `~/.gemini/antigravity-cli/{conversations,daemon,persistent_terminals}` | replaced by empty read-only mounts |
| Network | none, except domains allowed under `read_url` rules |

The research helper's sandbox is stricter. Its `run_command` config carries `"readOnlySandbox": true` and `"disableSandboxBypass": true`: the workspace, `/tmp` and its `brain` folder are read-only except for a scratch folder of its own. `curl` from the helper failed with "Could not resolve host: example.com".

The sandbox does not apply to the agent's other tools. `read_file`, `edit_file` and `write_file` run in `agy` itself: when the sandbox could not start any command, the research helper still read workspace files with `read_file`, a `.env` file among them. Google's docs say "Sensitive files such as `~/.ssh` and `.env` are blocked"; that holds for commands, not for the file tools.

Evidence:
- Binary: `devtools/ai/sandbox/exebox.(*FileTree).applyDefaultDeny`, `devtools/ai/sandbox/exebox.(*FileTree).denyAndPrune`, `devtools/ai/sandbox/exebox.(*Box).startLocalProxy`, `devtools/ai/sandbox/exebox.(*Box).writeProxyCert`, `jetski/cortex/command/sandboxproxy.(*AuditLogger).Close`; the helper's read-only mounts come from `jetski/cortex/command.(*goRunner).Run` through `jetski/cortex/command.readOnlySandboxScratch` and `jetski/cortex/command.demoteWritableMountsIfReadOnly`
- Prompt text: [terminal_sandbox.rendered.txt](../../resources/cli/binary-1.5.14/system_sections/terminal_sandbox.rendered.txt) ("The run_command tool executes commands inside a secure sandbox by default")
- Google: [Terminal sandbox](https://antigravity.google/docs/sandbox)

## The model is told the sandbox gives full access and exists to skip approval

The main agent's system prompt carries a `<terminal_sandbox>` section (3,225 bytes as rendered) when the sandbox is on. It describes the sandbox like this:

```text
- **Standard Sandbox Mode (BypassSandbox: false)**: By default has full read/write access to your workspace, your artifact and scratch directories (`<appDataDir>/brain/`, including `scratch/`, task logs, and transcripts), standard system binaries, and `/tmp`, but no network access and no access to other files outside the workspace unless added explicitly by the user.

The main purpose of the sandbox is to auto-run commands without needing the user's approval. Given the purpose of the sandbox, you shouldn't need to mention the sandbox unless brought up by the user.
```

Five rules follow: try sandboxed first, keep using the sandbox for anything that works there, keep `toolSummary` identical on a bypass retry ("Summaries should focus strictly on the user-facing operational goal and shouldn't mention execution modes of the tool like bypass sandbox"), split chains so only the one step that needs it runs outside, and prefer command shapes that "always allow" rules can prefix-match. The text does not mention that `.env` files or credentials are blocked.

Evidence:
- Prompt text: [terminal_sandbox.rendered.txt](../../resources/cli/binary-1.5.14/system_sections/terminal_sandbox.rendered.txt)
- Binary: `jetski/cortex/mixins.(*TerminalSandboxSection).ShouldInclude` decides whether the section is sent

## `BypassSandbox` asks for "elevated user permission", which in auto mode is a model's verdict

`run_command` takes a `BypassSandbox` boolean whenever the sandbox is on. Its description in the request:

```text
Set to true to run outside the sandbox (requires elevated user permission for auth exchange, network, or port binding).
```

A second text in the binary tells the agent to use it after a failure: "If you think the failure is because of running in the sandbox, you can run the command again with `BypassSandbox` set to true to request explicit user permission." A third refuses it for some commands: "This command includes call(s) to `%s`, which MUST run inside the sandbox, so `BypassSandbox` was not applied to this command. If you really need to run the other parts outside the sandbox, run them as a separate command."

What happens to the request depends on the mode. Outside auto mode, Google's docs say these requests "always require your approval unless the command matches a `command` allow rule". In auto mode the command adjudicator on Gemini 4 Hydrogen decides; `reversible: true` runs the command outside the sandbox with no prompt ([safety-reviews.md](safety-reviews.md#in-auto-mode-a-model-decides-which-commands-leave-the-sandbox-without-asking)). For personal accounts, a bypass that does reach you uses the ordinary command prompt; only business and enterprise accounts get the explicit "🔓 Allow sandbox bypass for command execution?" warning. The research helper's `run_command` has no `BypassSandbox` parameter.

When the sandbox cannot start a command, the result reads "Command failed to run: fork/exec <path to agy>: operation not permitted." followed by "No output from the command." The error names `agy`'s own executable, the sandbox launcher, rather than the command, and says nothing about the sandbox. The main agent worked it out after "Thought for 1m 9s", retried `git diff --stat` with `BypassSandbox: true`, the adjudicator returned `"reversible": true`, and the command ran outside the sandbox without a prompt. The research helper, with no bypass, guessed file paths with `read_file` instead, `.env` among them.

Evidence:
- Traffic: [the bypass retry and the adjudicator's verdict](../../resources/cli/traffic/review-verdicts.md#the-reversibility-check); [the `BypassSandbox` declaration](../../resources/cli/traffic/skip-permissions.md)
- Google: [Terminal sandbox](https://antigravity.google/docs/sandbox) ("CLI permissions integration", "Interactive prompts")

## Permission modes: an execution mode and a tool permission

Two settings decide what runs without asking. `agentMode` is the execution mode cycled with Shift+Tab or set with `--mode`: `auto`, `default` (stored as `""`), `accept-edits` and `plan`. `toolPermission` decides commands outside auto mode. Google's settings page defines its four values:

- `request-review` (default): "Prompts for your approval before running write, bash, web, or MCP tools, unless an allow rule covers the action."
- `proceed-in-sandbox`: "Runs terminal commands without prompting when Sandbox Mode is on and the command runs inside the terminal sandbox; otherwise prompts for review."
- `strict`: "Prompts before every tool action, including file reads, even when an allow rule matches (overrides `auto` mode)."
- `always-proceed`: "Runs terminal commands, web requests, and MCP tools without prompting, except actions that match an ask or deny rule (highest risk, use with caution; overrides `auto` mode)."

The sandbox itself is a third switch, `enableTerminalSandbox` (default `false`) or `--sandbox` ("--sandbox: enabling terminal sandbox for this session"). Auto mode turns it on. In `default` mode with the default settings, commands run outside the sandbox after you approve them. Allow, ask and deny rules (`command(...)`, `read_url(...)`, `write_file(...)`) apply in every mode and also set the sandbox's mounts and network allow-list. Each session reports its mode to Clearcut as `permission_mode`, `toolPermission` and `agentMode`.

Evidence:
- Google: [Settings](https://antigravity.google/docs/settings), [Choose an execution mode](https://antigravity.google/docs/cli/modes), [Terminal sandbox](https://antigravity.google/docs/sandbox)
- Binary: `jetski/cli/store.effectiveSandboxEnabled`, `jetski/cli/store.(*Manager).GetEnableTerminalSandbox`

## Auto mode: sandbox on, the adjudicator for everything that leaves it

Auto mode is the default "where `auto` mode is available" (Google's docs, since v1.5.7). The start screen says "Auto mode: edits & safe commands auto-approved". It approves workspace edits, runs commands in the sandbox, and sends bypass requests to the adjudicator, except for commands on the server's review-free lists in `auto-command-config`. Google's docs say auto mode "isn't currently available for business or enterprise accounts" and is not offered where the sandbox is unavailable.

Whether auto mode exists for an account is decided by server switches. `jetski/cli/backend.(*ServerBackend).AutoModeEnabled` and `jetski/cli/store.(*Store).AutoAgentModeAvailable` both require `enable-auto-permission-preset`, and the backend also requires `permission-v2`. `jetski/language_server/settings.(*JetskiHubSettingsStore).GatePreset` reads `auto-mode-permission-preset` (`"auto"`). `jetski/language_server.(*Server).maybeEnrollNewUserInAutoMode` reads `enroll-new-users-in-auto-mode` (false). `jetski/cli/store.(*Store).canSuggestAutoMode` offers auto mode in the permission prompt only if `cli-suggest-auto-mode-in-permission-prompt` is on (false). `jetski/admin_controls/admincontrols.(*Manager).IsPermissionPresetLocked` locks the preset when an admin control says so or `enforce-default-permission-preset` is on (false).

Evidence:
- Binary: the functions named above
- Server: [`enable-auto-permission-preset`](../../resources/cli/server-switches/INDEX.md#enable-auto-permission-preset), [`permission-v2`](../../resources/cli/server-switches/INDEX.md#permission-v2), [`auto-mode-permission-preset`](../../resources/cli/server-switches/INDEX.md#auto-mode-permission-preset), [`enroll-new-users-in-auto-mode`](../../resources/cli/server-switches/INDEX.md#enroll-new-users-in-auto-mode), [`cli-suggest-auto-mode-in-permission-prompt`](../../resources/cli/server-switches/INDEX.md#cli-suggest-auto-mode-in-permission-prompt), [`enforce-default-permission-preset`](../../resources/cli/server-switches/INDEX.md#enforce-default-permission-preset)
- Google: [Choose an execution mode](https://antigravity.google/docs/cli/modes)

## `--dangerously-skip-permissions` drops the sandbox, not the reviews

The flag sets the permission mode to `always-proceed`: "--dangerously-skip-permissions: auto-approving all tool permissions" (in print mode, "Print mode: --dangerously-skip-permissions set, auto-approving all tool permissions"). Headless runs without it deny what they cannot prompt for and point to it: "the %s tool(s) required approval that headless mode cannot prompt for, so they were auto-denied. Settings allow-rules do not apply; re-run with --dangerously-skip-permissions to auto-approve all tools."

With the flag, the agent's requests lose the `<terminal_sandbox>` section and `run_command` loses `BypassSandbox`. Commands run outside the sandbox: asked to "Run curl -sI https://example.com outside the sandbox", the agent ran it and got `HTTP/2 200`, then told the user "I cannot run commands outside the sandbox, so I ran curl -sI https://example.com within the standard sandboxed environment." The status bar kept showing `auto`, and Clearcut recorded `agentMode auto` beside `permission_mode always-proceed`.

The DeepMind monitor chain still ran. The listing, the edit, a test run and the `curl` each went to Flash-Lite ([safety-reviews.md](safety-reviews.md#the-deepmind-monitor-is-a-three-stage-chain-set-by-the-server)). Google's docs say deny and ask rules keep precedence over the flag.

Evidence:
- Traffic: [a session with the flag](../../resources/cli/traffic/skip-permissions.md)
- Google: [Choose an execution mode](https://antigravity.google/docs/cli/modes) ("global overrides such as `--dangerously-skip-permissions` (`always-proceed`)"), [Settings](https://antigravity.google/docs/settings)

## What the server can change

| Switch | Server value | What it controls |
|---|---|---|
| [`enable-auto-permission-preset`](../../resources/cli/server-switches/INDEX.md#enable-auto-permission-preset) | `true` | Whether auto mode exists |
| [`permission-v2`](../../resources/cli/server-switches/INDEX.md#permission-v2) | `true` | Required with the above for auto mode |
| [`auto-mode-permission-preset`](../../resources/cli/server-switches/INDEX.md#auto-mode-permission-preset) | `auto` | The preset auto mode uses |
| [`enroll-new-users-in-auto-mode`](../../resources/cli/server-switches/INDEX.md#enroll-new-users-in-auto-mode) | `false` | Puts new users in auto mode |
| [`enforce-default-permission-preset`](../../resources/cli/server-switches/INDEX.md#enforce-default-permission-preset) | `false` | Locks the permission preset |
| [`cli-suggest-auto-mode-in-permission-prompt`](../../resources/cli/server-switches/INDEX.md#cli-suggest-auto-mode-in-permission-prompt) | `false` | Offers auto mode from a permission prompt |
| [`policy-guardian-config`](../../resources/cli/server-switches/INDEX.md#policy-guardian-config) | `{"allowed": true, "deployment": "ANTIGRAVITY", "enable_irreversible_vetting": true, "enable_reversibility_adjudication": true}` | Turns on the reversibility adjudicator |
| [`auto-command-config`](../../resources/cli/server-switches/INDEX.md#auto-command-config) | `"system_allowlist": ["cd"]`, `"sandbox_system_allowlist"`: 14 commands including `rm`, `cp`, `mv`, `find` | Commands that skip review |
| [`allow-experimental-monitors`](../../resources/cli/server-switches/INDEX.md#allow-experimental-monitors) | per model, true for 17 entries | Turns on the DeepMind monitor chain |
| [`enable-rpc-sandbox-proxy`](../../resources/cli/server-switches/INDEX.md#enable-rpc-sandbox-proxy) | `false` | Read with the environment variable `JETSKI_ENABLE_RPC_SANDBOX_PROXY`, which takes precedence |
| [`enable-rpc-sandbox-proxy-allow-all`](../../resources/cli/server-switches/INDEX.md#enable-rpc-sandbox-proxy-allow-all) | `false` | Whether the sandbox's network proxy lets everything through |

The sandbox's own rules (mounts and network) are compiled into the binary and derived from your permission rules. The review chain, the adjudicator and the review-free command lists are configured by the server, and none of them has a local setting.

Evidence:
- Server: [server switch catalogue](../../resources/cli/server-switches/INDEX.md), [available models](../../resources/cli/server-switches/available-models.json)
- Binary: `jetski/cortex/command.IsRPCSandboxProxyEnabled` reads `JETSKI_ENABLE_RPC_SANDBOX_PROXY`, then `enable-rpc-sandbox-proxy`
