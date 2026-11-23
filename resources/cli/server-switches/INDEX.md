# Server switches

`agy` asks Google's server for these at every start (`listExperiments`). The response held 635 switches. Each section below says what `agy` 1.5.14 does with them; for the ones it reads, the row says what the switch controls and which function reads it. Long values are cut; the full response is [experiments.json](experiments.json). What the switches mean taken together is in [Server switches: what Google can change](../../../analysis/cli/server-switches.md).

| What `agy` does with them | Switches |
|---|---|
| [Read by `agy`](#read-by-agy) | 202 |
| [In the snapshot only](#in-the-snapshot-only) | 71 |
| [Not in `agy`: Antigravity names](#not-in-agy-antigravity-names) | 162 |
| [Not in `agy`: other Google products](#not-in-agy-other-google-products) | 200 |

## Read by `agy`

Code in `agy` 1.5.14 reads these. The snapshot value comes from the [built-in snapshot](../binary-1.5.14/configuration/generic-system-prompt-d040eb2.json), which `agy` uses in place of the server's values when the account has no Code Assist API access.

### Safety, permissions & data collection

| Switch | Server value | Snapshot value | What it does | Read by |
|---|---|---|---|---|
| <a id="allow-experimental-monitors"></a>`allow-experimental-monitors` | `false` | — | Sets `CommandAssessorConfig.AllowExperimentalMonitors`, which `agent_monitoring/jetski.NewToolEvaluatorsFromConfig` passes into the sonar tool-call evaluators. The built-in monitor config marks `agy_tool_safety`, `sir_flash_lite` and `severe_internal_risks_agy` as `experimental_only`. The server sends false here, but `fetchAvailableModels` sends true for 17 models, and per-model values replace this one, so the three monitors run for those models. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="allow-pss-experimental-monitors"></a>`allow-pss-experimental-monitors` | `false` | — | Sets `CommandAssessorConfig.AllowPssExperimentalMonitors`, the permission for sonar safety monitors marked `pss_experimental_only` in the monitor config. The server's false leaves those monitors off. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="auto-command-config"></a>`auto-command-config` | `{"system_allowlist": ["cd"], "sandbox_system_allowlist": ["h…` | `{"system_allowlist": ["cd"], "sandbox_system_allowlist": ["h…` | Fills `RunCommandToolConfig.AutoCommandConfig` with the commands that run without asking: `cd` everywhere, and `head`, `tail`, `mkdir`, `cd`, `cp`, `mv`, `cat`, `find`, `grep`, `rm`, `touch`, `less`, `clear`, `ls` inside the sandbox. The server's value matches the snapshot value. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="auto-mode-permission-preset"></a>`auto-mode-permission-preset` | `auto` | `auto` | Picks the preset auto mode resolves to when enable-auto-permission-preset is on: the value "vetted" selects a different preset; any other value, including "auto" (server and snapshot value), keeps auto. | `jetski/language_server/settings.(*JetskiHubSettingsStore).GatePreset` |
| <a id="cli-suggest-auto-mode-in-permission-prompt"></a>`cli-suggest-auto-mode-in-permission-prompt` | `false` | — | When on, and auto mode is available, the preset isn't locked and the conversation isn't remote, the CLI's permission prompt offers to switch to auto mode. The server sends false, so the prompt doesn't offer it. | `jetski/cli/store.(*Store).canSuggestAutoMode` |
| <a id="disable-command-assessor"></a>`disable-command-assessor` | `false` | — | CommandAssessorEnabled returns true only when enable-command-assessor is on and this switch is off; when it returns true, buildDefaultAgent adds the command_assessor pre- and post-tool hooks and augmentConversationalConfigWithExperiment fills in the command-assessor config that screens the commands the agent runs. The server sends false, so it does not switch the assessor off. | `jetski/cortex/config.CommandAssessorEnabled` |
| <a id="disable-stained-loas2-creds"></a>`disable-stained-loas2-creds` | `true` | `true` | Read by the sidecar manager and the language server's flag-update listener to decide whether stained LOAS2 credentials (Google's internal machine credentials) are disabled; the sandbox factory `jetski/cortex/command.newCLIBoxFactory.func1` uses the stained-credentials path. `getToolConfigOverrides` reads it and discards the result. The server's true matches the snapshot value. | `jetski/cortex/sidecars.(*SidecarManager).effectiveStainedLOAS2CredsDisabled` |
| <a id="enable-ask-custom-permission-tool"></a>`enable-ask-custom-permission-tool` | `false` | — | Adds the `ask_custom_permission` tool to the default agent and sets `CascadeToolConfig.AskCustomPermission`. The server's false leaves the tool out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-auto-permission-preset"></a>`enable-auto-permission-preset` | `true` | — | Makes Auto mode available: `AutoModeEnabled` requires it together with permissions v2, Hub settings keep the server's `auto` preset only while it is on, new users can be enrolled in Auto mode, and the default agent gets the `auto_run_safety_gate` hook (`jetski/cortex/autorun.NewSafetyGateHook`). The server sets it to true. | `jetski/cli/backend.(*ServerBackend).AutoModeEnabled` |
| <a id="enable-browser-subagent-high-stakes-consent"></a>`enable-browser-subagent-high-stakes-consent` | `false` | — | When the webview browser is enabled, this turns on the webview browser tool's high-stakes consent, which pauses actions that 'look high-stakes' (for example on payment checkout hosts) and asks the user for a one-time approval. The server's false leaves it off. | `jetski/cortex/customizations/builtin/agents.UseWebviewBrowserHighStakesConsent` |
| <a id="enable-browser-subagent-sensitive-site-ask"></a>`enable-browser-subagent-sensitive-site-ask` | `false` | — | When on, the permission manager treats a built-in list of sensitive sites (banks, health portals and similar) separately: 'Broad browser permissions and Turbo don't cover it, so the agent asks before it opens this site.' The server's false leaves broad browser grants covering those sites. | `jetski/cortex/permissions.(*permissionManager).sensitiveSiteMatchIfEnabled` |
| <a id="enable-browser-subagent-url-safety"></a>`enable-browser-subagent-url-safety` | `false` | — | When the webview browser is enabled, this turns on the webview browser tool's URL-safety option. The server sends false, so the option stays off. | `jetski/cortex/customizations/builtin/agents.UseWebviewBrowserURLSafety` |
| <a id="enable-citc-top-level-write-guard"></a>`enable-citc-top-level-write-guard` | `false` | — | Adds the `citc_top_level_write_guard` pre-tool hook, which denies writes that would create a new top-level entry in a CitC workspace (Google's internal cloud workspaces). The server's false leaves the hook out. | `jetski/cortex/core.citcTopLevelWriteGuardEnabled` |
| <a id="enable-command-assessor"></a>`enable-command-assessor` | `true` | `true` | Turns on the command assessor: buildDefaultAgent adds command_assessor pre-tool and post-tool hooks and the conversation config gets a command-assessor block that screens shell commands the agent runs. The server sends true, the same as the snapshot value. | `jetski/cortex/config.CommandAssessorEnabled` |
| <a id="enable-conversation-memory-monitor"></a>`enable-conversation-memory-monitor` | `false` | — | When on, the language server periodically samples and reports each conversation's memory use (CascadeManager.SampleAndReportConversationMemory) at the interval set by conversation-memory-sample-interval-minutes. The server sends false, so no sampling runs. | `jetski/language_server.(*Server).isConversationMemoryMonitorEnabled` |
| <a id="enable-conversation-scoped-permissions"></a>`enable-conversation-scoped-permissions` | `false` | — | When on, each conversation can carry its own permission preset: it is applied to that conversation's config and copied to forks. While it is off, setting it is rejected with 'permission_preset: conversation-scoped permissions are not enabled'. The server sends false. | `jetski/cortex.(*CascadeManager).conversationScopedPermissionsEnabled` |
| <a id="enable-per-conversation-telemetry-toggle"></a>`enable-per-conversation-telemetry-toggle` | `false` | — | Checked by the language server's UpdateConversationAnnotations RPC; it gates a per-conversation telemetry setting. The server sends false. | `jetski/language_server.(*Server).isPerConversationTelemetryToggleEnabled` |
| <a id="enable-permissioned-github"></a>`enable-permissioned-github` | `false` | — | Adds the `permissioned-github` skill to the default agent. The server's false leaves it out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-primes-tracing"></a>`enable-primes-tracing` | `false` | — | When on and telemetry isn't skipped (CodeAssistClient.SkipTelemetry), the language server's startup trace logger starts exporting traces. The server sends false, so startup traces aren't exported. | `jetski/language_server.CreateLanguageServerAndServe.func8` |
| <a id="enable-remote-agent-uberproxy-sso"></a>`enable-remote-agent-uberproxy-sso` | `false` | — | Read at startup and on each switch update, then stored for the Code Assist client's remote-agent base URL. In 1.5.14 that function returns https://aicode.googleapis.com whatever the stored value is (only the AICODE_ENDPOINT_URL environment variable changes it), so the switch has no observable effect. | `jetski/language_server.remoteAgentUberProxySSOEnabled` |
| <a id="enable-remote-control-hub-browser-stream"></a>`enable-remote-control-hub-browser-stream` | `false` | — | When on, remote-control sessions can serve and route the agent's browser view (browserHostCanServe, prepareSendUserBrowserRouting). The server sends false. | `jetski/language_server.(*Server).isRemoteControlHubBrowserStreamEnabled` |
| <a id="enable-remote-credentials-for-local-plugins"></a>`enable-remote-credentials-for-local-plugins` | `false` | — | When on, and enable-plugin-auth is also on, local plugins get remote credentials (activeRemotePluginCreds). The server sends false. | `jetski/language_server.remotePluginCredsEnabled` |
| <a id="enable-rpc-sandbox-proxy"></a>`enable-rpc-sandbox-proxy` | `false` | — | When on, or when the JETSKI_ENABLE_RPC_SANDBOX_PROXY environment variable is 1 or true, ApplyRPCSandboxProxy sends network traffic from sandboxed commands through agy's sandbox proxy, which enforces host rules ('sandbox proxy refused HTTP %s (rule: %s)'). The server's false leaves the proxy off unless the variable is set. | `jetski/cortex/command.IsRPCSandboxProxyEnabled` |
| <a id="enable-rpc-sandbox-proxy-allow-all"></a>`enable-rpc-sandbox-proxy-allow-all` | `false` | — | When the RPC sandbox proxy is on and sandboxed commands have network access, ApplySettingsToConfig adds '*' to the proxy's allowed hosts in some permission modes, so sandboxed commands can reach any host. The server's false keeps the host allowlist. | `jetski/cortex/settings.ApplySettingsToConfig` |
| <a id="enable-run-workflow-sandbox"></a>`enable-run-workflow-sandbox` | `false` | — | This name is only in the name table that populateExperimentsMap copies into each conversation's experiment map. No 1.5.14 code or template looks it up, so the server's false has no visible effect. | `jetski/cortex/config.populateExperimentsMap` |
| <a id="enable-scoped-csrf-tokens"></a>`enable-scoped-csrf-tokens` | `true` | `true` | Turns on scoped CSRF-token enforcement in the language server's RPC interceptor (it also defaults to on when no switch provider is present). Server and snapshot value are both true. | `jetski/language_server/interceptor.(*CsrfInterceptor).isScopedEnforcementEnabled` |
| <a id="enable-swarm-monitor"></a>`enable-swarm-monitor` | `true` | `true` | Adds the built-in `swarm_monitor` skill to the default agent when native swarm is on. The server's true matches the snapshot value, and the 1.5.14 main-agent prompt lists the skill. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-trustedcli-config-files"></a>`enable-trustedcli-config-files` | `false` | — | Gates trusted-CLI definitions loaded from secplugins config files: LoadSecPlugins passes this name as an activation gate, and activationGatesPass rejects every such CLI while it is off. Trusted CLIs get their own handling (a dedicated sandbox, injected auth secrets, a sandbox escape). The server's false keeps file-defined trusted CLIs inactive. | `jetski/cortex/shared.TrustedCLI.activationGatesPass` |
| <a id="enable-workspace-trust"></a>`enable-workspace-trust` | `false` | — | Turns on the workspace-trust store passed to customization loading. It is only wired when the language server runs in `hub` mode, so it doesn't reach the CLI; the server sends false. | `jetski/language_server.workspaceTrustFlagEnabled` |
| <a id="enforce-default-permission-preset"></a>`enforce-default-permission-preset` | `false` | — | When on, the permission preset is locked to the default (also locked if admin controls lock it), and admin-control checks and turbo-mode exclusions follow from it. The server sends false. | `jetski/admin_controls/admincontrols.(*Manager).IsPermissionPresetLocked` |
| <a id="enforce-telemetry-disabled"></a>`enforce-telemetry-disabled` | `false` | — | When on (and a startup condition holds), the language server locks telemetry off: SkipTelemetry returns true and the disabled setting is saved. The server sends false, so telemetry follows the user's setting. | `jetski/language_server.CreateLanguageServerAndServe.func5` |
| <a id="enroll-new-users-in-auto-mode"></a>`enroll-new-users-in-auto-mode` | `false` | `true` | When on, with permission-v2 and enable-auto-permission-preset also on and no admin control applying, a newly signed-in user is given the auto permission preset (SeedAutoPermissionPreset). The server sends false, overriding the built-in true, so new users aren't enrolled. | `jetski/language_server.(*Server).maybeEnrollNewUserInAutoMode` |
| <a id="jetski-autonomous-mode"></a>`jetski-autonomous-mode` | `false` | — | This name is only in the name table that populateExperimentsMap copies into each conversation's experiment map. No 1.5.14 code or template looks it up, so the server's false has no visible effect. | `jetski/cortex/config.populateExperimentsMap` |
| <a id="jetski-shared-beyond-session-id"></a>`jetski-shared-beyond-session-id` | `false` | — | Read in the session-ID function the language server hands to its model API clients (setUpClients). The server sends false; what changes when it is on wasn't established. | `jetski/language_server.newSharedSessionIDFn.func1` |
| <a id="jetski-sidecar-declared-permissions"></a>`jetski-sidecar-declared-permissions` | `false` | `true` | Read by the sidecar manager (managed projects, project-permission sync, scheduled tasks), by plugin installation, and by the default-agent builder, which adds the `automation` skill only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI. The server's false switches these paths off against the built-in true. | `jetski/cortex/sidecars.(*SidecarManager).UsesSidecarManagedProjects` |
| <a id="log-artifacts-config"></a>`log-artifacts-config` | `{"enabled": true, "hideNominalToolSteps": false, "hidePlanne…` | `{"enabled": true, "hideNominalToolSteps": false, "hidePlanne…` | Turns on log artifacts: the default agent gets the `conversation_transcript` prompt section, and `TrajectoryConversionConfig.LogArtifactsConfig` is set with `maxBytesPerStep` 4096, `maxBytesPerToolArg` 2048 and no step hiding. The server's value matches the snapshot value. | `jetski/cortex/config.LogArtifactsEnabled` |
| <a id="permission-v2"></a>`permission-v2` | `true` | — | Turns on permissions v2: it sets `PermissionConfig.PermissionsV2` for tools, allows the grants-v2 migration, and is required for Auto mode, which is then refused for `gateway` sign-ins and left to admin controls for `adc` and `gcp` sign-ins. The server sets it to true. | `jetski/cli/backend.(*ServerBackend).permissionsV2` |
| <a id="plugin-kill-switch"></a>`plugin-kill-switch` | `` | — | Holds a server list of plugins, with optional version regexes, that agy treats as kill-switched: it marks them in the marketplace catalogue, refuses to install or upgrade them, and checks installed plugins against the list before loading their hooks and rules. The server sends an empty value. | `jetski/cortex/customizations.ParsePluginKillSwitch` |
| <a id="policy-guardian-config"></a>`policy-guardian-config` | `{"allowed": true, "deployment": "ANTIGRAVITY", "enable_irrev…` | — | `allowed: true` turns on Policy Guardian, which adds the `command_assessor` hook to the default agent, and the rest of the value (`deployment: ANTIGRAVITY`, `enable_irreversible_vetting`, `enable_reversibility_adjudication`) fills `CommandAssessorConfig.PolicyGuardianConfig`. There is no snapshot value, so the server turns the reviewer on. | `jetski/cortex/config.PolicyGuardianEnabled` |
| <a id="record-error-sampling-rates"></a>`record-error-sampling-rates` | `{       "error_match_to_sample_rate": {         "LS_CRASH": …` | — | Maps error names to sampling rates for the language server's error reporting (RecordError and the telemetry error reporter). The server sends LS_CRASH and ui_client_jetski_chat_setup_failed at rate 1.0, so every one of those errors is reported. | `jetski/language_server.ShouldSampleError` |
| <a id="remote-control-companion-url"></a>`remote-control-companion-url` | `https://antigravity.google.com` | — | Web address returned for remote control (GetRemoteControlInfo); when unset or empty it falls back to https://antigravity.google.com, the same as the server value. | `jetski/language_server.(*Server).GetRemoteControlInfo` |
| <a id="remote-control-proxy-server-url"></a>`remote-control-proxy-server-url` | `jetski-webchannel.googleapis.com:443` | — | Sets the relay address that the remote-control connection uses. The server sends jetski-webchannel.googleapis.com:443, and the flag watcher applies changes while agy runs. | `jetski/language_server.(*Server).getProxyServerURLWithState` |
| <a id="remote-control-setting-enabled"></a>`remote-control-setting-enabled` | `true` | — | Makes remote control (/remote-control, --remote-control) available unless an admin control disables it. A Mendel update listener re-reads the switch and updates the remote-control connection while agy runs. The server sends true. | `jetski/language_server.(*Server).isRemoteControlEnabled` |
| <a id="remote-control-silence-timeout-seconds"></a>`remote-control-silence-timeout-seconds` | `60` | — | How long a remote-control connection may stay silent before it is treated as failed: 60 seconds when unset, and 60 from the server. | `jetski/language_server.(*Server).getRemoteControlSilenceTimeout` |
| <a id="remote-control-version"></a>`remote-control-version` | `v2` | — | The value "v2" selects version 2 of remote control, which changes the connection setup and instance ID. The server sends "v2". | `jetski/language_server.(*Server).isRemoteControlV2` |
| <a id="remote-control-webrtc-mesh"></a>`remote-control-webrtc-mesh` | `true` | — | Remote-control connections use a WebRTC mesh when on. The server sends true. | `jetski/language_server.(*Server).isWebRTCMeshEnabled` |
| <a id="rpc-telemetry-allowlist"></a>`rpc-telemetry-allowlist` | `{}` | — | Lists language-server RPC method names whose calls are reported to telemetry by the RPC telemetry interceptor. The server sends an empty list, so no RPC calls are reported. | `jetski/language_server/interceptor.(*rpcTelemetryInterceptor).shouldReportTelemetry` |
| <a id="sonar-monitors-config"></a>`sonar-monitors-config` | `` | — | A non-empty value replaces `CommandAssessorConfig.SonarMonitorsConfigTextproto`, the definition of the sonar safety monitors, falling back to `default-sonar-monitors-config`. The server sends it empty and does not send the fallback, so the monitors come from the built-in config. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="third-party-tools-disclaimer-text"></a>`third-party-tools-disclaimer-text` | `{           "default": "Google does not control the availabi…` | `{           "default": "Google does not control the availabi…` | Supplies the notice the CLI shows when installing marketplace plugins, which says in part "Your data from third party developer tools connected to your Antigravity instance may be shared with Google." The server value matches the snapshot value. | `jetski/cli/store.(*PluginsManager).ThirdPartyToolsNotice` |

### Tools, plugins & integrations

| Switch | Server value | Snapshot value | What it does | Read by |
|---|---|---|---|---|
| <a id="agy-featured-plugins"></a>`agy-featured-plugins` | `` | `{"cards": [{       "id": "featured",       "customizationIds…` | getMarketplaceFeatured parses this switch into the featured plugin cards shown in the marketplace. The built-in value lists Firebase, Notion, Context7, Linear, Dynatrace, Stripe and egm; the server sends an empty string, and whether that empties the cards or falls back to the built-in list is not established. | `jetski/language_server.(*Server).getMarketplaceFeatured` |
| <a id="agy-plugin-marketplaces"></a>`agy-plugin-marketplaces` | `{"values": ["antigravity-plugins-official=https://www.gstati…` | `["antigravity-plugins-official=https://www.gstatic.com/antig…` | Lists the plugin marketplace catalogues (antigravity-plugins-official and antigravity-plugins-partners, both on www.gstatic.com). The CLI hides its plugin command when the list is empty, and admin controls can disable the marketplace. The server value matches the snapshot value. | `jetski/cli/store.thirdPartyMarketplaces` |
| <a id="agy-plugin-shelves"></a>`agy-plugin-shelves` | `{"shelves": [{   "id": "google-workspace",   "displayName": …` | `{"shelves": [{   "id": "google-workspace",   "displayName": …` | marketplaceIndex parses this switch into the plugin marketplace's shelves. The server's value, the same as the snapshot's, adds a 'Google Workspace' shelf with the Docs, Sheets, Slides, Drive, Calendar and Gmail plugins. | `jetski/language_server.(*Server).marketplaceIndex` |
| <a id="cascade-use-sed-edit-tool"></a>`cascade-use-sed-edit-tool` | `false` | — | When true, sets `CodeToolConfig.UseSedEdit`. The server's false leaves it unset. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="cli-enable-search-everything"></a>`cli-enable-search-everything` | `true` | — | Turns on full-text search in the CLI conversation picker and @-mention suggestions. The server sends true. | `jetski/cli/model.baseModel.fullTextSearchActive` |
| <a id="disable-tool-hook-notices"></a>`disable-tool-hook-notices` | `false` | — | When true, sets `CascadeExecutorConfig.DisableToolHookNotices`. The server's false leaves tool-hook notices on. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="enable-agy-hub-cua-foreground"></a>`enable-agy-hub-cua-foreground` | `false` | — | Turns on foreground control (screenshots, clicks, typing, keys) for the computer-use agent that operates Mac apps through AGY Hub; with it off, only background accessibility actions and window screenshots are used. The server sends false. | `jetski/language_server.CreateLanguageServerAndServe.func14` |
| <a id="enable-agy-hub-cua-macos"></a>`enable-agy-hub-cua-macos` | `false` | — | Turns on computer use for AGY Hub on macOS: the language server stores it with the script-mode and foreground variants, and the default agent gets the matching subagent only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI. The server's false leaves it off. | `jetski/language_server.CreateLanguageServerAndServe.func14` |
| <a id="enable-agy-hub-cua-script-mode"></a>`enable-agy-hub-cua-script-mode` | `true` | `true` | Turns on script mode for the computer-use agent that operates Mac apps through AGY Hub. Server and snapshot value are both true. | `jetski/language_server.CreateLanguageServerAndServe.func14` |
| <a id="enable-ark"></a>`enable-ark` | `false` | — | Sets `AntigravityBrowserToolConfig.EnableArk` and adds the `browser-vision` subagent to the default agent. The server's false leaves both off. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-citc-workspace-hygiene-rule"></a>`enable-citc-workspace-hygiene-rule` | `true` | `true` | populateExperimentsMap copies it into each conversation's experiment map, and the built-in configuration sets it to true, but no 1.5.14 code path or prompt template reads it. The server's true matches the default and has no visible effect. | `jetski/cortex/config.populateExperimentsMap` |
| <a id="enable-customization-load-error-notice"></a>`enable-customization-load-error-notice` | `false` | `true` | When on, the language server's GetCustomizationStates RPC includes notices about customizations that failed to load. The server sends false, overriding the built-in true, so the notices are dropped; the CLI doesn't call this RPC. | `jetski/language_server.(*Server).GetCustomizationStates` |
| <a id="enable-dictation-rework"></a>`enable-dictation-rework` | `false` | — | Controls how the language server's StreamAudioTranscription (dictation) RPC builds speech-recognition options. When on, it uses the language codes and recognition context the client forwards; the server sends false, so the language server derives the recognition context from the request itself (extractAsrContext). | `jetski/language_server.(*Server).isDictationReworkEnabled` |
| <a id="enable-directory-local-discovery"></a>`enable-directory-local-discovery` | `false` | — | Adds the `directory_customizations` pre-hook, which loads customizations found in the directories the agent works in. The server's false leaves the hook out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-generative-hooks"></a>`enable-generative-hooks` | `false` | — | When on, JSON hooks in prompt mode run through executePromptModeHook. While it is off, they fail with 'prompt hooks are not currently supported'. The server sends false. | `jetski/cortex/customization/hooks/jsonhook.(*HookHandler).Execute` |
| <a id="enable-git-vcs-ui"></a>`enable-git-vcs-ui` | `false` | `true` | Only read by built-in test fixtures (builtinfixtures); no production code reads it in 1.5.14. | `jetski/language_server/builtinfixtures.createReviewDiffFixture` |
| <a id="enable-hook-status"></a>`enable-hook-status` | `true` | `true` | Sets `CascadeExecutorConfig.EnableHookStatus` to true, the same as the snapshot value. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="enable-list-plugin-accounts-tool"></a>`enable-list-plugin-accounts-tool` | `false` | `true` | Adds the `list_plugin_accounts` tool only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI and sets `CascadeToolConfig.ListPluginAccounts`. The server's false switches it off against the built-in true. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-marketplace-search-tool"></a>`enable-marketplace-search-tool` | `false` | `true` | Adds the `featured_plugins` prompt section and the `search_marketplace` tool only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI, and lets the language server warm up featured plugins unless an admin has disabled the marketplace. The server's false switches this off against the built-in true. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-marketplace-user-count"></a>`enable-marketplace-user-count` | `false` | — | Sets whether the ranked marketplace index shows user counts; it only applies when enable-plugin-ranking builds that index. The server sends false. | `jetski/language_server.(*Server).viaRAP` |
| <a id="enable-mcp-apps"></a>`enable-mcp-apps` | `false` | `true` | Calls `McpManager.SetMcpAppsEnabled` with its value and adds the `mcp-app-development` skill only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI. The server's false disables MCP apps against the built-in true. | `jetski/language_server.CreateLanguageServerAndServe.func15` |
| <a id="enable-mcp-non-blocking-turn-load"></a>`enable-mcp-non-blocking-turn-load` | `true` | `true` | Sets `McpToolConfig.NonBlockingTurnLoad`. The server's true matches the snapshot value. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="enable-mcp-oauth-redirect"></a>`enable-mcp-oauth-redirect` | `false` | — | Chooses how sign-in to OAuth-protected MCP servers works: when on, a local loopback callback server receives the redirect; when off, the user pastes the code. The server sends false, so MCP sign-in uses the paste flow. | `jetski/language_server.CreateLanguageServerAndServe.func16` |
| <a id="enable-mcp-plugin-auth"></a>`enable-mcp-plugin-auth` | `true` | `true` | When this switch and enable-plugin-auth are both on, processPluginMcpServers attaches plugin auth providers to MCP servers declared by plugins, so those servers can be called with stored plugin credentials. A watcher re-applies the gate when the server value changes. The server sends true, the default. | `jetski/cortex/customizations.(*Manager).mcpPluginAuthEnabled` |
| <a id="enable-persistent-terminals"></a>`enable-persistent-terminals` | `true` | `true` | Sets `RunCommandToolConfig.EnablePersistentMode`, so `run_command` uses the persistent terminal runner. The server's true matches the snapshot value. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="enable-plugin-auth"></a>`enable-plugin-auth` | `true` | `true` | Turns on plugin authentication: it sets `RunCommandToolConfig.EnablePluginAuth` and is checked for plugin MCP auth, remote plugin credentials and context-provider auth. The server's true matches the snapshot value. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="enable-plugin-ranking"></a>`enable-plugin-ranking` | `false` | — | When on, the language server builds a ranked marketplace index backed by the Code Assist SearchRemoteAgentExtensions call, which it uses for marketplace search, featured plugins, customization and onboarding personas. The server sends false, so no remote ranking index is built. | `jetski/language_server.(*Server).viaRAP` |
| <a id="enable-plugins"></a>`enable-plugins` | `false` | `true` | Gates plugin features: the default agent gets the `migrate-agent-settings` and `plugin` skills only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI, and the language server checks it before importing projects from a URL, upgrading marketplace and Build-with-Google plugins, building onboarding personas and running plugin migrations. The server's false switches these off against the built-in true. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-product-api-skill"></a>`enable-product-api-skill` | `false` | — | Adds the `conversations` skill (manage local conversations over HTTP+JSON) on the CLI and Hub surfaces. The server's false leaves it out, and the 1.5.14 main-agent prompt does not list it. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-programmatic-tools"></a>`enable-programmatic-tools` | `false` | — | When on, NewRunWorkflowTool builds the script tools for the run_workflow tool, which lets the agent run scripts that call tools programmatically. The server sends false. | `jetski/cortex/core/tools/runworkflow.NewRunWorkflowTool` |
| <a id="enable-pty"></a>`enable-pty` | `true` | `true` | Sets `RunCommandToolConfig.EnablePty`, so commands run in a pseudo-terminal. The server's true matches the snapshot value. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="enable-remote-local-browser-hands"></a>`enable-remote-local-browser-hands` | `false` | — | Read in the language server's GetHubOAuthAccessToken RPC; when on, and the user is signed in with a Google account rather than an API key, the RPC takes an additional token path. The server sends false; the CLI doesn't call this RPC. | `jetski/language_server.(*Server).GetHubOAuthAccessToken` |
| <a id="enable-retroactive-projects-migration"></a>`enable-retroactive-projects-migration` | `true` | — | After sign-in, runs a one-time migration that sorts existing conversations into projects (projectsmigration.MaybeRunRetroactiveProjectsMigration). It only runs when the language server runs in `hub` mode, so it doesn't apply to the CLI; the server sends true. | `jetski/language_server.(*Server).postAuthInit` |
| <a id="enable-ripgrep-path-search"></a>`enable-ripgrep-path-search` | `true` | `true` | When on, the language server's SearchFiles RPC finds paths with ripgrep (search.LocalBackend.SearchPath). Server and snapshot value are both true. | `jetski/language_server.(*Server).SearchFiles` |
| <a id="enable-skill-search-tool"></a>`enable-skill-search-tool` | `false` | — | Adds the `skill_search` tool to the default agent and sets `CascadeToolConfig.SkillSearch`. The server's false leaves the tool out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-ui-sidecars"></a>`enable-ui-sidecars` | `false` | `true` | Adds the `ui-plugin-development` and `ui-plugin-navigation` skills only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI. The server's false switches them off against the built-in true. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-update-focus-tool"></a>`enable-update-focus-tool` | `false` | — | Adds the `update_focus` tool to the default agent only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI. The server's false leaves it out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-update-steps-tool"></a>`enable-update-steps-tool` | `false` | — | Adds the `update_steps` tool to the default agent only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI. The server's false leaves it out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-url-artifacts"></a>`enable-url-artifacts` | `false` | — | Gates the 'Bookmarking URLs with URL Artifacts' section of the artifacts system-prompt template, which tells the model to create .url.json artifacts for relevant links. The server's false leaves the section out. | `jetski/prompt/template_provider/templateprovider.(*systemPromptData).ExperimentEnabled` |
| <a id="enable-voice-relay"></a>`enable-voice-relay` | `true` | `true` | Makes CLI live mode (/voice live, a spoken conversation using the microphone) available; if the switch turns off during a session, live mode ends with "live mode was turned off". The server and the snapshot value are both true; the language server's own voice relay route is always off when it runs for the CLI. | `jetski/cli/store.(*Store).LiveModeAvailable` |
| <a id="enable-voice-remote"></a>`enable-voice-remote` | `false` | — | The language server's /voice/ route allows voice in conversations on the local backend; for a conversation on a remote backend it allows voice only when this switch is on. The server sends false, so voice is refused for remote conversations. | `jetski/language_server.(*Server).isVoiceAllowedIn` |
| <a id="enable-voice-spanner"></a>`enable-voice-spanner` | `false` | — | When on, the language server can list and fetch voice-conversation trajectories through the remote agent proxy, and CreateDebugBundle includes them. While it is off, those calls return nothing or '%s is off'. The server sends false. | `jetski/language_server.rapVoiceTrajectories.ListRemoteAgentVoiceTrajectories` |
| <a id="jetski-unified-customizations-panel-enabled"></a>`jetski-unified-customizations-panel-enabled` | `false` | — | This name is only in the name table that populateExperimentsMap copies into each conversation's experiment map. No 1.5.14 code or template looks it up, so the server's false has no visible effect. | `jetski/cortex/config.populateExperimentsMap` |
| <a id="media-compression-config"></a>`media-compression-config` | `` | — | Fills `CascadeConfig.MediaCompressionConfig` when set. The server sends an empty value, so nothing is set. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="pin-depot-head"></a>`pin-depot-head` | `false` | — | When on, paths under /google/src/files/head/depot/ are rewritten to a pinned depot root (fs.PinGoogleDepotHeadPath). These are Google-internal source paths, so the switch has no effect for outside users; the server sends false. | `jetski/language_server.CreateLanguageServerAndServe.func17` |
| <a id="science-plugin-split-targets"></a>`science-plugin-split-targets` | `{}` | `["antigravity-plugins-official/plugin/science-overview", "an…` | Targets for a migration that, when plugins are enabled, splits the official science plugin into five plugins (science-overview, genomics, structural-biology, scientific-literature, chemistry). The server sends an empty list, overriding the snapshot value, so the migration doesn't run. | `jetski/language_server/migrations.splitTargets` |
| <a id="tool-output-max-bytes"></a>`tool-output-max-bytes` | `10240` | `10240` | Sets `ToolOutputConfig.MaxOutputBytes`, capping tool output at 10240 bytes. The server's value matches the snapshot value. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="voice-engine-addr"></a>`voice-engine-addr` | `default` | `default` | Address of the voice engine used by the language server's voice handling. The server and snapshot value are both "default". | `jetski/language_server.(*Server).voiceEngineAddr` |

### Agents, sessions & workflows

| Switch | Server value | Snapshot value | What it does | Read by |
|---|---|---|---|---|
| <a id="agent-retry-config"></a>`agent-retry-config` | `` | — | Fills `CascadePlannerConfig.RetryConfig` when set. The server sends an empty value, so nothing is set. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="agent-script-reroute"></a>`agent-script-reroute` | `` | — | CascadeManager.New reads it when setting up the agent. The server sends an empty string, and what a non-empty value does is not established here. | `jetski/cortex/config.GetAgentScriptReroute` |
| <a id="battle-mode-max-turns"></a>`battle-mode-max-turns` | `0` | — | Sets `BattleModeConfig.MaxTurns`; the `battle_mode_max_turns` pre-hook, which counts user turns in a battle-mode fork, is added only for values above 0. The server's 0 leaves the hook out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="cascade-agent-api-config"></a>`cascade-agent-api-config` | `` | — | Fills `CascadeConfig.AgentApiConfig` when set. The server sends an empty value, so nothing is set. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="cli-disable-stream-conversation-updates-rehydration"></a>`cli-disable-stream-conversation-updates-rehydration` | `false` | — | When on, the CLI's conversation update stream is opened with rehydration disabled. The server sends false, so rehydration stays on. | `jetski/cli/backend.(*ServerBackend).StreamConversationUpdates` |
| <a id="cli-enable-conversation-groups"></a>`cli-enable-conversation-groups` | `false` | — | The only reader is a test helper that writes the switch into a store's flag map; no production code reads it in 1.5.14. | `jetski/cli/store.(*Store).SetCustomGroupsForTest` |
| <a id="convenient-multi-agent-coordination"></a>`convenient-multi-agent-coordination` | `false` | — | When on, invoke_subagent gains a Count field that spawns up to 10 identical workers, send_message accepts '@children' to broadcast to all active direct subagents, and manage_subagents gains an extended action set. The server sends false. | `oneharness/internal/tools.invokeSubagentDefinition` |
| <a id="deepagent-arm"></a>`deepagent-arm` | `` | — | When enable-deepagent is on, a non-empty value supplies the deepagent definition in place of the built-in one. The server sends an empty string. | `jetski/cortex/core/components/config.deepAgent` |
| <a id="default_subagent_interaction_timeout_seconds"></a>`default_subagent_interaction_timeout_seconds` | `60` | `60` | Sets how long a subagent or swarm worker waits for the user to answer an interaction request; getCascadeConfigForTrajectory applies it to subagent conversations as UserInteractionTimeoutSeconds unless one is already set. The server's 60 matches the default. | `jetski/cortex/core/native_swarm/nativeswarm.(*manageSwarmTool).subagentInteractionTimeout` |
| <a id="deprecate-workflows"></a>`deprecate-workflows` | `false` | `true` | Adds the `migrate-workflows` skill on the CLI and Hub surfaces. The server's false switches it off against the built-in true. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-advanced-builtin-workflow"></a>`enable-advanced-builtin-workflow` | `false` | — | Read by the default-agent builder together with `enable-run-workflow-tool` to gate one more built-in workflow entry. The server's false leaves it out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-agent-plugins-spec"></a>`enable-agent-plugins-spec` | `true` | `true` | Lets agy load plugins whose manifest declares the agent-plugins.org 1.0.0 schema; it is also required for 1.1.0. The server sends true, the default. | `jetski/cortex/customizations.(*Manager).pluginSpecVersion` |
| <a id="enable-agent-plugins-spec-1-1"></a>`enable-agent-plugins-spec-1-1` | `false` | — | When this switch and enable-agent-plugins-spec are both on, pluginSpecVersion accepts plugin manifests that declare the agent-plugins.org 1.1.0 schema. The server's false means agy rejects 1.1.0 manifests. | `jetski/cortex/customizations.(*Manager).agentPluginsSpec1Point1Enabled` |
| <a id="enable-browser-subagent-auto-invoke"></a>`enable-browser-subagent-auto-invoke` | `false` | `true` | When the webview browser is enabled, this sets the auto-invoke flag in the browser tool config. The snapshot value is true, but the server sends false, and the webview browser is off anyway. | `jetski/cortex/config.BrowserSubagentAutoInvokeEnabled` |
| <a id="enable-browser-subagent-glic-host"></a>`enable-browser-subagent-glic-host` | `false` | — | This is an alternative to enable-browser-subagent-webview for turning on the webview browser. It also lets the language server hand browser requests to a host browser (browserHostCanServe, CheckDevToolsActivePort). The server sends false. | `jetski/cortex/config.BrowserSubagentWebviewEnabled` |
| <a id="enable-browser-subagent-milestone-collab"></a>`enable-browser-subagent-milestone-collab` | `false` | — | When on, the browser subagent's description gains a 'Collaboration contract' that tells the main agent to open every prompt to it with Goal, Done when, In scope, Out of scope and Target lines. Browser host-facts pre-tool and stop hooks are added, and workspace user information is left out of its prompt. The server sends false. | `jetski/cortex/customizations/builtin/agents.withMilestoneCollab` |
| <a id="enable-browser-subagent-v2"></a>`enable-browser-subagent-v2` | `true` | `true` | Sets `AntigravityBrowserToolConfig.EnableBrowserSubagentV2`, adds the `browser` subagent to the default agent, and is required for the browser-subagent webview. The server's true matches the snapshot value. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-browser-subagent-webmcp"></a>`enable-browser-subagent-webmcp` | `false` | — | In the webview browser's script mode, this lets browser scripts list and call WebMCP tools that web pages expose (page.webmcp.listTools/callTool). The server's false turns it off. | `jetski/cortex/customizations/builtin/agents.UseWebviewBrowserWebMCP` |
| <a id="enable-browser-subagent-webview"></a>`enable-browser-subagent-webview` | `false` | `true` | Together with enable-browser-subagent-v2 (or the glic-host switch), this turns on the webview browser subagent and its tool config, including its model and auto-invoke settings; the webview-script and webview-ux options also depend on it. The snapshot value is true, but the server sends false and turns the webview browser off. | `jetski/cortex/config.BrowserSubagentWebviewEnabled` |
| <a id="enable-browser-subagent-webview-script"></a>`enable-browser-subagent-webview-script` | `false` | `true` | When the webview browser is enabled, this turns on script mode, where the browser subagent drives pages with a scripted page API. The snapshot value is true; the server sends false. | `jetski/cortex/customizations/builtin/agents.UseWebviewBrowserScript` |
| <a id="enable-browser-subagent-webview-ux"></a>`enable-browser-subagent-webview-ux` | `false` | `true` | When the webview browser is enabled, this turns on the webview browser tool's UX option. The snapshot value is true, but the server sends false and turns it off. | `jetski/cortex/customizations/builtin/agents.UseWebviewBrowserUx` |
| <a id="enable-conversation-search-v2"></a>`enable-conversation-search-v2` | `false` | `true` | When on, the language server builds its conversation search index after sign-in and its SearchConversations RPC uses it. The server sends false, overriding the built-in true; the CLI's own conversation search uses a separate index. | `jetski/language_server.(*Server).maybeInitConversationSearch` |
| <a id="enable-deepagent"></a>`enable-deepagent` | `false` | — | When on, buildDefaultAgent adds the 'deepagent' subagent ('Experimental. Deep reasoning agent for complex coding and research projects that need planning and rigorous verification.'). The server's false leaves it out. | `jetski/cortex/core/components/config.deepAgent` |
| <a id="enable-fork-artifact-streaming"></a>`enable-fork-artifact-streaming` | `false` | — | When a conversation is forked, this selects whether its artifacts are copied to the fork by streaming. The server sends false. | `jetski/cortex.(*CascadeManager).streamForkArtifacts` |
| <a id="enable-fork-provenance"></a>`enable-fork-provenance` | `false` | — | When on, conversation summaries (updateSummary, ListConversationSummaries) record fork provenance, meaning which conversation a fork came from. The server's false leaves it out. | `jetski/cortex.(*CascadeManager).shouldPopulateForkProvenance` |
| <a id="enable-fork-with-subagents"></a>`enable-fork-with-subagents` | `false` | — | When on, a conversation that has invoked subagents can be forked. While it is off, fork.requireNoSubagents refuses with 'forking is not supported for conversations that have invoked subagents'. The server sends false. | `jetski/cortex.(*CascadeManager).allowForkWithSubagents` |
| <a id="enable-image-generator-subagent"></a>`enable-image-generator-subagent` | `true` | `true` | When subagent tools are enabled (invoke-subagent-config), buildDefaultAgent adds the image-generator subagent. The server sends true, the default. | `jetski/cortex/config.ImageGeneratorSubagentEnabled` |
| <a id="enable-native-swarm"></a>`enable-native-swarm` | `true` | `true` | Turns on native swarm: the default agent gets the `swarm_post_message`, `swarm_read_board` and `swarm_wait` tools and the swarm hooks (`swarm_board_head`, `swarm_wait_check`, `swarm_park_guard`), the `native_swarm` skill is registered, and `agy swarm live` is unhidden and loads sessions. The server's true matches the snapshot value. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-resume-conversations-from-restart"></a>`enable-resume-conversations-from-restart` | `false` | — | When on, after sign-in the language server resumes conversations that a restart interrupted (CascadeManager.ResumeConversationsFromRestart). The server sends false, so interrupted conversations aren't resumed automatically. | `jetski/language_server.(*Server).postAuthInit` |
| <a id="enable-review"></a>`enable-review` | `false` | — | When on, buildDefaultAgent adds the 'review-agent' subagent ('Experimental. Deep code review agent for auditing changes, surfacing bugs, and verifying correctness.') along with a matching review-agent slash entry. The server's false leaves it out. | `jetski/cortex/core/components/config.reviewAgent` |
| <a id="enable-run-workflow-tool"></a>`enable-run-workflow-tool` | `true` | `true` | Adds the `run_workflow` tool, its prompt section and the `run-workflow` skill to the default agent. The server's true matches the snapshot value. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-swarm-narrator"></a>`enable-swarm-narrator` | `false` | — | Adds the built-in `swarm_narrator` skill to the default agent only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI, and only with `enable-native-swarm` on. The server's false keeps it out. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-swarm-overview"></a>`enable-swarm-overview` | `false` | `true` | Turns on the swarm overview in the `agy swarm live` board (`live.runtime.func5` returns this switch). The server's false switches it off against the built-in true; the `swarm_pane` skill still appears in the 1.5.14 main-agent prompt. | `jetski/cli/swarm/live.runtime.func5` |
| <a id="enable-teamwork-subagent"></a>`enable-teamwork-subagent` | `true` | — | Makes the built-in teamwork agents (colosseum, document_review, external, swe_light) available when the model provider is one of two supported types, and adds the teamwork subagent to the default agent. The server sets it to true. | `jetski/cortex/customizations/builtin/agents.init.func7` |
| <a id="invoke-subagent-config"></a>`invoke-subagent-config` | `{"enabled": true, "always_inherit_model": false}` | `{"enabled": true, "always_inherit_model": false}` | `enabled: true` gives the default agent the `invoke_subagent`, `define_subagent` and `manage_subagents` tools, and `always_inherit_model: false` fills `CascadeToolConfig.InvokeSubagent`. The server's value matches the snapshot value. | `jetski/cortex/config.SubagentToolsEnabled` |
| <a id="native-swarm-channels-local"></a>`native-swarm-channels-local` | `false` | — | Allows named channels on locally stored swarm boards. While it is off, channel actions in the swarm tools fail ('... on local boards (Mendel flag native-swarm-channels-local is off)') and swarm members share only #main. The server sends false. | `jetski/cortex/core/native_swarm/nativeswarm.channelFlags.allow` |
| <a id="native-swarm-channels-spanner"></a>`native-swarm-channels-spanner` | `false` | — | Allows named channels on swarm boards stored in Spanner. When it is off, channel actions in the swarm tools fail with '%w on %s boards (Mendel flag %s is off)'. The server sends false. | `jetski/cortex/core/native_swarm/nativeswarm.channelFlags.allow` |
| <a id="native-swarm-spanner-board"></a>`native-swarm-spanner-board` | `false` | — | With the swarm backend left on 'auto', this switch decides whether a swarm board should live in Spanner instead of locally; the JETSKI_SWARM_BACKEND environment variable ('local' or 'spanner') overrides it. If Spanner isn't available, chooseBackend falls back to a local board. The server's false keeps boards local. | `jetski/cortex/core/native_swarm/nativeswarm.SpannerWanted` |
| <a id="review-arm"></a>`review-arm` | `` | — | When enable-review is on, a non-empty value is parsed as a JSON agent config that replaces the built-in review-agent definition. The server sends an empty string, so the built-in definition would be used. | `jetski/cortex/core/components/config.reviewAgent` |

### Models, prompts & context

| Switch | Server value | Snapshot value | What it does | Read by |
|---|---|---|---|---|
| <a id="browser-subagent-model"></a>`browser-subagent-model` | `gemini-3.8-flash-low` | — | Sets the model the webview browser subagent uses. The server sends gemini-3.8-flash-low, but it applies only when the webview browser is enabled. | `jetski/cortex/config.BrowserSubagentWebviewModel` |
| <a id="cascade-knowledge-config"></a>`cascade-knowledge-config` | `{"enabled": false, "min_turns_between_knowledge_generation":…` | `{"enabled": false, "min_turns_between_knowledge_generation":…` | Fills `CascadePlannerConfig.KnowledgeConfig`; the server's value (`enabled: false`, `min_turns_between_knowledge_generation: 10000`) keeps knowledge generation off, as does the snapshot value. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="checkpoint-variant"></a>`checkpoint-variant` | `claude-1m_840k` | `claude-1m_840k` | Names the checkpoint variant that selectCheckpointExperimentKey uses to pick the checkpoint override config (applyFeatureProviderCheckpointOverrideConfig). The server's 'claude-1m_840k' matches the snapshot value. | `jetski/cortex/config.selectCheckpointExperimentKey` |
| <a id="conversation-memory-sample-interval-minutes"></a>`conversation-memory-sample-interval-minutes` | `5` | — | Interval for the conversation memory monitor: 5 minutes, both when unset and from the server. It only matters when enable-conversation-memory-monitor is on, and the server has that off. | `jetski/language_server.(*Server).conversationMemorySampleInterval` |
| <a id="customization-token-budget"></a>`customization-token-budget` | `20000` | `20000` | Sets `CascadePlannerConfig.CustomizationTokenBudget` to 20000, the same as the snapshot value. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="deprecate-ephemeral-messages"></a>`deprecate-ephemeral-messages` | `false` | — | CreateSerializer copies this switch into an option on the serializer that turns the trajectory into model input; its name says that option deprecates ephemeral messages. The server's false keeps the current serialization. | `oneharness/internal/core/integration.CreateSerializer` |
| <a id="disable-system-message-merging"></a>`disable-system-message-merging` | `false` | — | When true, sets `TrajectoryConversionConfig.DisableSystemMessageMerging`. The server's false leaves system-message merging as built. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="disable-system-message-tag"></a>`disable-system-message-tag` | `false` | — | When true, sets `TrajectoryConversionConfig.DisableSystemMessageTag`. The server's false leaves it unset. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="disable-teamwork-forced-flash-model"></a>`disable-teamwork-forced-flash-model` | `false` | — | In Antigravity, the teamwork subagent defaults to the Flash model tier unless this switch is on. The server sends false, so teamwork runs on Flash by default. | `jetski/cortex/config.TeamworkDefaultsToFlash` |
| <a id="enable-compact-slash-command"></a>`enable-compact-slash-command` | `true` | `true` | Makes the /compact slash command available when the agent has the context-summary component; NewContextSummaryHook reads it too. The server sends true, the default. | `jetski/cortex/slashcommands.init.func2` |
| <a id="enable-context-role"></a>`enable-context-role` | `false` | — | When true, sets `TrajectoryConversionConfig.EnableContextRole`. The server's false leaves it unset. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="enable-continue-after-injection"></a>`enable-continue-after-injection` | `false` | — | When off, the local backend rejects a user message that asks to continue after an injection with "continue_after_injection is not enabled or allowed for this user and client". The server sends false. | `jetski/language_server/backend/local.(*Backend).SendUserCascadeMessage` |
| <a id="enable-model-capacity-exhausted-retries"></a>`enable-model-capacity-exhausted-retries` | `true` | `true` | When on, model calls retry on capacity-exhausted errors if the switch parameter retry_model_capacity_exhausted allows it. Server and snapshot value are both true. | `jetski/language_server/modelapiccpa.(*Client).Generate` |
| <a id="enable-orchestrator-model-selection"></a>`enable-orchestrator-model-selection` | `true` | `true` | Lets the CLI model selector offer orchestrator models, with roles taken from orchestrator-model-roles; when off, orchestrator selection isn't offered. Server and snapshot value are both true. | `jetski/cli/store.(*Store).Orchestrator` |
| <a id="enable-prompt-cache-keep-alive"></a>`enable-prompt-cache-keep-alive` | `false` | — | When on, model calls through the Code Assist model client send keep-alive pings to hold the prompt cache, up to the count in the switch parameter prompt_cache_keep_alive_max_pings. The server sends false, so no keep-alive pings are sent. | `jetski/language_server/modelapiccpa.keepAlivePings` |
| <a id="enable-run-workflow-call-tool-prompts"></a>`enable-run-workflow-call-tool-prompts` | `false` | — | This name is only in the name table that populateExperimentsMap copies into each conversation's experiment map when the server sends it. Nothing in 1.5.14 code or in its prompt templates looks it up, so the server's false changes nothing. | `jetski/cortex/config.populateExperimentsMap` |
| <a id="enable-subagent-dropdown-models"></a>`enable-subagent-dropdown-models` | `false` | — | When on, populatePlatformModels builds a subagent model list from the account's available models (SubagentModelsFromAvailableModels) for model choice in subagents. The server's false skips that list. | `jetski/cortex.(*CascadeManager).populatePlatformModels` |
| <a id="enable-work-efficiently-reminder"></a>`enable-work-efficiently-reminder` | `true` | `true` | Adds the `work_efficiently_reminder` pre-hook, which injects "Reminder: work efficiently. In this turn, issue in parallel every independent search, read and edit or write you need next. ..." after single-tool-call turns. The server's true matches the snapshot value. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="generic-system-prompt"></a>`generic-system-prompt` | `true` | `true` | Switches system-prompt templates to their general-purpose wording ('You are Antigravity, a powerful agentic AI assistant designed by the Google Deepmind team' instead of the coding-assistant wording) and drops some code-specific instructions. It also makes the user-information section say 'Files relating to the user's requests' instead of 'Code relating to the user's requests'. The server sends true, the default. | `jetski/cortex/mixins.(*UserInformationSection).Content` |
| <a id="jetski-generative-ui-prompt"></a>`jetski-generative-ui-prompt` | `false` | — | Gates the 'Interactive HTML Widgets' section of the artifacts system-prompt template, which tells the model it can render interactive HTML widgets with the generative_ui skill. The section appears only when jetski-generative-ui is also on and the generative_ui skill is mounted; the server's false leaves it out. | `jetski/prompt/template_provider/templateprovider.(*systemPromptData).ExperimentEnabled` |
| <a id="max-tokens-per-step"></a>`max-tokens-per-step` | `16384` | `16384` | Sets `TrajectoryConversionConfig.MaxTokensPerStep` to 16384, the same as the snapshot value. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="native-swarm-suggested-models"></a>`native-swarm-suggested-models` | `` | — | Holds a JSON list of suggested worker models and descriptions that manage_swarm shows when listing models and uses to pick a default worker model. The server sends an empty value, so the model dropdown alone decides. | `jetski/cortex/core/native_swarm/nativeswarm.(*manageSwarmTool).modelDescriptions` |
| <a id="orchestrator-model-roles"></a>`orchestrator-model-roles` | `{       "orchestrators": [         "gemini-4-neon",         …` | `{       "orchestrators": [         "gemini-4-neon",         …` | Lists which models the CLI offers as orchestrators (gemini-4-neon, gemini-4-carbon, gemini-4-argon, claude-opus-5-5) and as executors (gemini-4-hydrogen, claude-sonnet-5-5) when orchestrator selection is on. The server value matches the snapshot value. | `jetski/cli/store.(*Store).Orchestrator` |
| <a id="rules-token-budget"></a>`rules-token-budget` | `20000` | `20000` | Sets `CascadePlannerConfig.RulesTokenBudget` to 20000, the same as the snapshot value. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="send-subagent-initial-prompt-as-message"></a>`send-subagent-initial-prompt-as-message` | `true` | `true` | Sets `InvokeSubagentToolConfig.SendInitialPromptAsMessage` to true, the same as the snapshot value. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="show-model-selection-change"></a>`show-model-selection-change` | `true` | `true` | When false, sets `TrajectoryConversionConfig.ShowModelSelectionChange` to false. The server's true keeps model changes in the conversation sent to the model. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="summary-store-reads"></a>`summary-store-reads` | `false` | — | When on, the CLI lists conversations from the summary store and starts a reconcile of that store at startup. The server sends false, so conversation lists come from the regular path. | `jetski/cli/backend.(*ServerBackend).summaryStoreReadsEnabled` |

### Infrastructure, quotas & other

| Switch | Server value | Snapshot value | What it does | Read by |
|---|---|---|---|---|
| <a id="continuous-pprof-cpu-duration-seconds"></a>`continuous-pprof-cpu-duration-seconds` | `10` | — | Length of each CPU profile: 10 seconds, both when unset and from the server. It only matters when enable-continuous-pprof is on, and the server has that off. | `jetski/language_server/profiling.(*Profiler).CPUDuration` |
| <a id="continuous-pprof-cpu-interval-minutes"></a>`continuous-pprof-cpu-interval-minutes` | `90` | — | CPU-profile interval for the continuous profiler: 30 minutes when unset, 90 from the server. It only matters when enable-continuous-pprof is on, and the server has that off. | `jetski/language_server/profiling.(*Profiler).CPUInterval` |
| <a id="continuous-pprof-heap-interval-minutes"></a>`continuous-pprof-heap-interval-minutes` | `90` | — | Heap-profile interval for the continuous profiler: 30 minutes when unset, 90 from the server. It only matters when enable-continuous-pprof is on, and the server has that off. | `jetski/language_server/profiling.(*Profiler).HeapInterval` |
| <a id="continuous-pprof-mem-check-interval-minutes"></a>`continuous-pprof-mem-check-interval-minutes` | `1` | — | How often the profiler checks memory against the threshold: 1 minute, both when unset and from the server. It only matters when enable-continuous-pprof is on, and the server has that off. | `jetski/language_server/profiling.(*Profiler).MemoryCheckInterval` |
| <a id="continuous-pprof-mem-cooldown-minutes"></a>`continuous-pprof-mem-cooldown-minutes` | `30` | — | Minimum gap between memory-triggered profile uploads: 30 minutes, both when unset and from the server. It only matters when enable-continuous-pprof is on, and the server has that off. | `jetski/language_server/profiling.(*Profiler).MemoryUploadCooldown` |
| <a id="continuous-pprof-mem-threshold-gib"></a>`continuous-pprof-mem-threshold-gib` | `16` | — | Memory threshold in GiB that triggers a heap profile: none when unset, 16 GiB from the server. It only matters when enable-continuous-pprof is on, and the server has that off. | `jetski/language_server/profiling.(*Profiler).MemoryThresholdBytes` |
| <a id="contrib-experiment-variant"></a>`contrib-experiment-variant` | `` | — | Names a contrib variant that resolveContribVariant applies to the default agent's components when ResolveDefaultAgent builds it. The server sends an empty string, so no variant is applied. | `jetski/cortex/core/components/config.resolveContribVariant` |
| <a id="cortex-snapshot-recording"></a>`cortex-snapshot-recording` | `false` | — | When true, sets `CascadeExecutorConfig.CortexSnapshotRecording`. The server's false leaves it unset. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |
| <a id="cortex-snapshot-revert"></a>`cortex-snapshot-revert` | `false` | — | Turns on snapshot-based revert: `CascadeManager.snapshotRevertEnabled` gates `RevertToStep`, `GetRevertPreview` and artifact-directory forking, and `CascadeExecutorConfig.CortexSnapshotRevert` is set. The server's false leaves it off. | `jetski/cortex.(*CascadeManager).snapshotRevertEnabled` |
| <a id="dark-launch-aicode-quota-status"></a>`dark-launch-aicode-quota-status` | `false` | — | Sets shadow mode for the aicode quota-status lookup in the quota-summary fetch (CodeAssistClient.SetShadowModeAICodeFetchQuotaStatus). The server sends false. | `jetski/language_server.registerMendelUpdateListener.func1` |
| <a id="dbtrajectory-gen-metadata-cache-ttl-seconds"></a>`dbtrajectory-gen-metadata-cache-ttl-seconds` | `0` | — | Cache lifetime for generation metadata in the conversation database; zero or unset means no lifetime is applied. The server sends 0, the same as unset. | `jetski/language_server.genMetadataCacheTTLFromFeatureProvider` |
| <a id="dbtrajectory-step-cache-ttl-seconds"></a>`dbtrajectory-step-cache-ttl-seconds` | `0` | — | Cache lifetime for conversation steps in the conversation database; zero or unset means no lifetime is applied. The server sends 0, the same as unset. | `jetski/language_server.stepCacheTTLFromFeatureProvider` |
| <a id="enable-continuous-pprof"></a>`enable-continuous-pprof` | `false` | — | Turns on the language server's continuous profiler, which captures CPU and heap profiles and uploads them, plus a memory-threshold worker; the intervals come from the continuous-pprof-* switches. The server sends false, so no profiles are captured. | `jetski/language_server/profiling.(*Profiler).IsEnabled` |
| <a id="enable-cost-accumulator"></a>`enable-cost-accumulator` | `false` | — | When on, loaded conversations get the cost-accounting component (WithCostAccounting), and the agent executor builds its cost accumulator. The server sends false. | `jetski/cortex.(*CascadeManager).shouldEnableCostAccounting` |
| <a id="enable-enterprise-remote-ai"></a>`enable-enterprise-remote-ai` | `false` | — | Decides whether the backend router may send enterprise conversations to the cloud backend; refused requests fail with "enterprise remote conversations are not enabled for this user". The server sends false. | `jetski/language_server.(*Server).remoteEnterpriseAIEnabled` |
| <a id="enable-inference-traffic-migration"></a>`enable-inference-traffic-migration` | `false` | — | When on, model calls (GenerateContent, streaming generation) and loadCodeAssist go to https://aicode.googleapis.com instead of Cloud Code (cloudcode-pa.googleapis.com); the USE_AICODE environment variable overrides it. The server sends false, so inference stays on Cloud Code. | `jetski/language_server.registerMendelUpdateListener.func1` |
| <a id="enable-remote-ai"></a>`enable-remote-ai` | `false` | — | Decides whether the language server's backend router may send conversations to cloud-hosted (remote) backends; a refused request fails with "cloud conversations are not enabled for this user". The server sends false, so conversations stay on the local backend. | `jetski/language_server.(*Server).remoteAIEnabled` |
| <a id="shadow-call-aicode-fetch-licenses"></a>`shadow-call-aicode-fetch-licenses` | `false` | — | Passed with use-aicode-fetch-licenses to CodeAssistClient.SetFetchLicensesFlags; when on, the licence lookup is also sent to the aicode backend in the background and compared with the Cloud Code result. The server sends false. | `jetski/language_server.registerMendelUpdateListener.func1` |
| <a id="sign-out-missing-aicode-scope"></a>`sign-out-missing-aicode-scope` | `true` | — | Turns on a check that the sign-in token carries the scope the aicode backend needs. For personal consumer accounts the CLI then warns: "Your sign-in is missing permissions that are now required, so some requests may fail. Run /logout to sign in again." The server sends true. | `jetski/cli/backend.(*ServerBackend).canWarnMissingAICodeScope` |
| <a id="use-aicode-fetch-licenses"></a>`use-aicode-fetch-licenses` | `false` | — | When on, the account's licences are fetched from the aicode backend instead of Cloud Code (CodeAssistClient.SetFetchLicensesFlags). The server sends false. | `jetski/language_server.registerMendelUpdateListener.func1` |
| <a id="use-aicode-jetski-endpoint"></a>`use-aicode-jetski-endpoint` | `false` | — | When on, Antigravity's own resource, list and request calls go to the aicode host that corresponds to the configured Cloud Code endpoint (for production, https://aicode.googleapis.com). The server sends false. | `jetski/cli/backend.NewServerBackend.func1` |
| <a id="use-aicode-mendel"></a>`use-aicode-mendel` | `true` | — | Routes the ListExperiments call that fetches these switches to https://aicode.googleapis.com, with a fallback to Cloud Code. The server sends true. | `jetski/language_server.registerMendelUpdateListener.func1` |
| <a id="use-aicode-preprod-endpoint"></a>`use-aicode-preprod-endpoint` | `false` | — | When on, a configured preprod-daily Cloud Code endpoint is mapped to https://preprod-aicode.sandbox.googleapis.com; production endpoints map to https://aicode.googleapis.com either way. The server sends false; it only matters for Google's preprod endpoints. | `jetski/language_server.registerMendelUpdateListener.func1` |
| <a id="user-interaction-timeout-seconds"></a>`user-interaction-timeout-seconds` | `0` | — | Sets `CascadeToolConfig.UserInteractionTimeoutSeconds` to the server's 0. | `jetski/cortex/config.getToolConfigOverrides` |

### Interface, onboarding & promotion

| Switch | Server value | Snapshot value | What it does | Read by |
|---|---|---|---|---|
| <a id="agy-cli-announcement-message"></a>`agy-cli-announcement-message` | `` | — | Text the CLI stores as its announcement message (store.(*Store).AnnouncementMessage) each time switches refresh. The server sends an empty string, so there is no announcement. | `jetski/cli/store.(*Manager).refreshExperiments` |
| <a id="agy-onboarding-personas"></a>`agy-onboarding-personas` | `` | `{"personas": [{       "id": "engineering", "displayName": "S…` | getOnboardingPersonas parses this switch into onboarding personas (the built-in value starts with 'Software Engineering') with suggested plugins and starter prompts, served only when enable-plugins is on. The server sends an empty string; whether that falls back to the built-in personas is not established. | `jetski/language_server.(*Server).getOnboardingPersonas` |
| <a id="cli-enable-transcript-mode"></a>`cli-enable-transcript-mode` | `false` | — | Makes transcript mode available in the CLI conversation view. The server sends false, so transcript mode is unavailable. | `jetski/cli/model.(*ConversationModel).transcriptModeEnabled` |
| <a id="cli-seasonal-theme-periods"></a>`cli-seasonal-theme-periods` | `{"values": ["halloween=2026-10-23..2026-11-01"]}` | — | Sets the dates of seasonal CLI themes (a themed banner and spinner). The server sends halloween=2026-10-23..2026-11-01 in place of the built-in calendar. | `jetski/cli/store.ThemeSchedule` |
| <a id="enable-authenticated-ui-bootstrap"></a>`enable-authenticated-ui-bootstrap` | `true` | `true` | When on, the language server leaves the CSRF token out of the index page it serves to its web UI (shouldRedactIndexCSRFToken). Server and snapshot value are both true; this only concerns the UI the language server serves, not the CLI. | `jetski/language_server.(*Server).isAuthenticatedUIBootstrapEnabled` |
| <a id="enable-developer-mode"></a>`enable-developer-mode` | `false` | — | Gates the language server's SetMendelFlagOverrides RPC, which lets a client override server switches locally; with the server value false it fails with "Mendel flag overrides require developer mode". The other reader is the mock-data test interceptor. | `jetski/language_server.(*Server).SetMendelFlagOverrides` |
| <a id="enable-enhanced-file-viewer"></a>`enable-enhanced-file-viewer` | `false` | — | Gates the language server's GetDefinition and GetReferences RPCs for the file viewer. The server sends false; the CLI doesn't call these RPCs, so this concerns the desktop UI only. | `jetski/language_server.(*Server).isEnhancedFileViewerEnabled` |
| <a id="enable-file-explorer-tab"></a>`enable-file-explorer-tab` | `false` | — | Only read by built-in test fixtures (builtinfixtures); no production code reads it in 1.5.14. | `jetski/language_server/builtinfixtures.singleFileFixture` |
| <a id="enable-help-slash-command"></a>`enable-help-slash-command` | `false` | — | Controls whether the /help slash command is available in a conversation. The server's false hides it. | `jetski/cortex/slashcommands.init.func14` |
| <a id="enable-input-autocomplete"></a>`enable-input-autocomplete` | `false` | — | When on, the language server's GetInputCompletion RPC returns input completions from installed plugins. The server sends false; the CLI doesn't call this RPC, so the effect is limited to the desktop UI. | `jetski/language_server.(*Server).GetInputCompletion` |
| <a id="enable-owl-slash-command"></a>`enable-owl-slash-command` | `true` | — | Adds the owl slash command and the `DeepInvestigator` subagent to the default agent. The server sets it to true. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="enable-timeout-notification"></a>`enable-timeout-notification` | `false` | — | Sets `RunCommandToolConfig.EnableTimeoutNotification`, which adds the optional `NotificationTimeoutSeconds` parameter to the `run_command` tool ("If the command runs for longer than this duration ... the system will notify you"). With the server's false the parameter is absent. | `jetski/cortex/config.getToolConfigOverrides` |
| <a id="jetski-cli-announcement-message"></a>`jetski-cli-announcement-message` | `` | — | Same role as agy-cli-announcement-message, but read only when the CLI's product name is "jetski"; agy reads the agy- switch. The server sends an empty string. | `jetski/cli/store.(*Manager).refreshExperiments` |
| <a id="jetski-generative-ui"></a>`jetski-generative-ui` | `false` | `true` | Adds the `generative_ui` skill only on the Hub surface (AGENT_SURFACE_HUB), never in the terminal CLI. The server's false switches it off against the built-in true. | `jetski/cortex/core/components/config.buildDefaultAgent` |
| <a id="use-slash-plan"></a>`use-slash-plan` | `true` | `true` | Sets `AgenticModeConfig.UseSlashPlan` to true in the planner config, the same as the snapshot value. | `jetski/cortex/config.augmentConversationalConfigWithExperiment` |

## In the snapshot only

The built-in snapshot has a value for these, but no code in `agy` 1.5.14 reads them.

| Switch | Server value | Snapshot value |
|---|---|---|
| <a id="CASCADE_USE_REPLACE_CONTENT_EDIT_TOOL"></a>`CASCADE_USE_REPLACE_CONTENT_EDIT_TOOL` | `{"max_fuzzy_edit_distance_fraction": 0.001, "allow_partial_r…` | — |
| <a id="battle-winner-survey-percent"></a>`battle-winner-survey-percent` | `50` | `50` |
| <a id="best-of-nudge-dismiss-cooldown-days"></a>`best-of-nudge-dismiss-cooldown-days` | `10` | `10` |
| <a id="best-of-nudge-length-threshold"></a>`best-of-nudge-length-threshold` | `150` | `150` |
| <a id="cascade-conversation-history-config"></a>`cascade-conversation-history-config` | `{"enabled": true, "max_conversations": 20}` | `{"enabled": true, "max_conversations": 20}` |
| <a id="context-engine-config"></a>`context-engine-config` | `{}` | `{}` |
| <a id="enable-audio-transcription"></a>`enable-audio-transcription` | `false` | `true` |
| <a id="enable-aux-pane-redesign"></a>`enable-aux-pane-redesign` | `false` | `true` |
| <a id="enable-aux-pane-redesign-extension"></a>`enable-aux-pane-redesign-extension` | `false` | `true` |
| <a id="enable-base-ui-tooltips"></a>`enable-base-ui-tooltips` | `true` | `true` |
| <a id="enable-battle-mode"></a>`enable-battle-mode` | `false` | — |
| <a id="enable-battle-mode-custom-agents"></a>`enable-battle-mode-custom-agents` | `true` | `true` |
| <a id="enable-browser-element-picker"></a>`enable-browser-element-picker` | `false` | `true` |
| <a id="enable-context-menu-system"></a>`enable-context-menu-system` | `false` | `true` |
| <a id="enable-conversation-groups"></a>`enable-conversation-groups` | `false` | — |
| <a id="enable-conversation-navigator"></a>`enable-conversation-navigator` | `false` | `true` |
| <a id="enable-conversation-navigator-chapter-titles"></a>`enable-conversation-navigator-chapter-titles` | `true` | `true` |
| <a id="enable-conversation-only-revert"></a>`enable-conversation-only-revert` | `false` | `true` |
| <a id="enable-conversation-selection-quoting"></a>`enable-conversation-selection-quoting` | `false` | `true` |
| <a id="enable-customization-kind-agent"></a>`enable-customization-kind-agent` | `true` | `true` |
| <a id="enable-customization-kind-hooks"></a>`enable-customization-kind-hooks` | `true` | `true` |
| <a id="enable-customization-kind-mcp"></a>`enable-customization-kind-mcp` | `true` | `true` |
| <a id="enable-customization-kind-plugin"></a>`enable-customization-kind-plugin` | `true` | `true` |
| <a id="enable-customization-kind-rule"></a>`enable-customization-kind-rule` | `true` | `true` |
| <a id="enable-customization-kind-skill"></a>`enable-customization-kind-skill` | `true` | `true` |
| <a id="enable-customization-kind-workflow"></a>`enable-customization-kind-workflow` | `true` | `true` |
| <a id="enable-feature-manager-keybindings"></a>`enable-feature-manager-keybindings` | `false` | `true` |
| <a id="enable-fig-vcs-ui"></a>`enable-fig-vcs-ui` | `true` | `true` |
| <a id="enable-github-decorator"></a>`enable-github-decorator` | `false` | `true` |
| <a id="enable-gob-cog-vcs-ui"></a>`enable-gob-cog-vcs-ui` | `true` | `true` |
| <a id="enable-gob-repo-vcs-ui"></a>`enable-gob-repo-vcs-ui` | `true` | `true` |
| <a id="enable-hub-builtin-browser"></a>`enable-hub-builtin-browser` | `false` | `true` |
| <a id="enable-jetbox-terminal-v2"></a>`enable-jetbox-terminal-v2` | `false` | `true` |
| <a id="enable-keybinding-customization"></a>`enable-keybinding-customization` | `false` | `true` |
| <a id="enable-new-themes"></a>`enable-new-themes` | `false` | `true` |
| <a id="enable-notebook-rendering"></a>`enable-notebook-rendering` | `false` | `true` |
| <a id="enable-notifications-settings"></a>`enable-notifications-settings` | `false` | `true` |
| <a id="enable-pdf-viewer"></a>`enable-pdf-viewer` | `false` | `true` |
| <a id="enable-permissions-selector"></a>`enable-permissions-selector` | `false` | `true` |
| <a id="enable-plugin-card-auto-resume"></a>`enable-plugin-card-auto-resume` | `false` | `true` |
| <a id="enable-plugin-context-providers"></a>`enable-plugin-context-providers` | `false` | `true` |
| <a id="enable-plugin-sidebar-section"></a>`enable-plugin-sidebar-section` | `false` | `true` |
| <a id="enable-project-4k"></a>`enable-project-4k` | `false` | `true` |
| <a id="enable-pseudo-project"></a>`enable-pseudo-project` | `true` | `true` |
| <a id="enable-reconcile-summaries"></a>`enable-reconcile-summaries` | `false` | `true` |
| <a id="enable-remote-preview-wake"></a>`enable-remote-preview-wake` | `true` | `true` |
| <a id="enable-remote-terminal-predictive-echo"></a>`enable-remote-terminal-predictive-echo` | `true` | `true` |
| <a id="enable-role-onboarding"></a>`enable-role-onboarding` | `false` | `true` |
| <a id="enable-settings-error-card"></a>`enable-settings-error-card` | `true` | `true` |
| <a id="enable-side-question-rework"></a>`enable-side-question-rework` | `false` | `true` |
| <a id="enable-sidecar-sdk"></a>`enable-sidecar-sdk` | `false` | `true` |
| <a id="enable-sidecars"></a>`enable-sidecars` | `false` | `true` |
| <a id="enable-split-view"></a>`enable-split-view` | `false` | `true` |
| <a id="enable-ssh-remote"></a>`enable-ssh-remote` | `false` | `true` |
| <a id="enable-subagent-hub"></a>`enable-subagent-hub` | `true` | `true` |
| <a id="enable-subagent-user-messaging"></a>`enable-subagent-user-messaging` | `false` | `true` |
| <a id="enable-swarm-graph-tab"></a>`enable-swarm-graph-tab` | `true` | `true` |
| <a id="enable-swarm-raw-tab"></a>`enable-swarm-raw-tab` | `false` | — |
| <a id="enable-user-message-bubbles"></a>`enable-user-message-bubbles` | `false` | `true` |
| <a id="enable-vscode-injection-removal"></a>`enable-vscode-injection-removal` | `false` | `true` |
| <a id="markdown-table-v2"></a>`markdown-table-v2` | `false` | `true` |
| <a id="max-best-of-n"></a>`max-best-of-n` | `8` | `8` |
| <a id="max-media-attachments"></a>`max-media-attachments` | `15` | `15` |
| <a id="project-4k-mode-copy"></a>`project-4k-mode-copy` | `` | `{           "project4k": {             "title": "Knowledge W…` |
| <a id="remove-conversation-history"></a>`remove-conversation-history` | `false` | `true` |
| <a id="scheduled-tasks-v2"></a>`scheduled-tasks-v2` | `false` | `true` |
| <a id="shadow-call-aicode-mendel"></a>`shadow-call-aicode-mendel` | `true` | `true` |
| <a id="sidecar-gateway"></a>`sidecar-gateway` | `false` | `true` |
| <a id="sidecar-iframe-prewarm"></a>`sidecar-iframe-prewarm` | `false` | `true` |
| <a id="use-component-rewrite"></a>`use-component-rewrite` | `true` | `true` |
| <a id="use-core-direct"></a>`use-core-direct` | `true` | `true` |

## Not in `agy`: Antigravity names

The `agy` 1.5.14 binary doesn't contain these names.

| Switch | Server value |
|---|---|
| <a id="1p-projects-v5"></a>`1p-projects-v5` | `false` |
| <a id="agy-hub-announcement-message"></a>`agy-hub-announcement-message` | `` |
| <a id="ambient_disabled_models"></a>`ambient_disabled_models` | `fable-5.2-*,claude-fable-5-2*` |
| <a id="ambient_injection_enabled"></a>`ambient_injection_enabled` | `true` |
| <a id="ambient_persistent_injection_enabled"></a>`ambient_persistent_injection_enabled` | `false` |
| <a id="ambient_prefetch_enabled"></a>`ambient_prefetch_enabled` | `false` |
| <a id="ambient_query_max_thought_chars"></a>`ambient_query_max_thought_chars` | `0` |
| <a id="ambient_query_max_thought_steps"></a>`ambient_query_max_thought_steps` | `0` |
| <a id="ambient_subagent_read_only"></a>`ambient_subagent_read_only` | `false` |
| <a id="apply-trajectory-acls"></a>`apply-trajectory-acls` | `true` |
| <a id="best-of-n-default-model-id"></a>`best-of-n-default-model-id` | `` |
| <a id="cascade-enable-messaging"></a>`cascade-enable-messaging` | `true` |
| <a id="cli-enable-agent-view"></a>`cli-enable-agent-view` | `false` |
| <a id="cli-enable-btw-threads"></a>`cli-enable-btw-threads` | `false` |
| <a id="cli-enable-jetmon"></a>`cli-enable-jetmon` | `false` |
| <a id="cli-enable-live-mode"></a>`cli-enable-live-mode` | `false` |
| <a id="cli-enable-vetted-permission"></a>`cli-enable-vetted-permission` | `false` |
| <a id="collect-x20-quotas-on-checkin"></a>`collect-x20-quotas-on-checkin` | `false` |
| <a id="configure-artifact-width"></a>`configure-artifact-width` | `false` |
| <a id="context-window-selector"></a>`context-window-selector` | `false` |
| <a id="default-magic-workspace-vcs-jj"></a>`default-magic-workspace-vcs-jj` | `false` |
| <a id="disable-loop-detection"></a>`disable-loop-detection` | `true` |
| <a id="disable-search-tools"></a>`disable-search-tools` | `false` |
| <a id="enable-abfs-workspace-ui"></a>`enable-abfs-workspace-ui` | `false` |
| <a id="enable-agent-omnitrace"></a>`enable-agent-omnitrace` | `false` |
| <a id="enable-agent-team"></a>`enable-agent-team` | `false` |
| <a id="enable-antigravity-api-keys"></a>`enable-antigravity-api-keys` | `false` |
| <a id="enable-artifact-inline-follow-up"></a>`enable-artifact-inline-follow-up` | `false` |
| <a id="enable-ask-permission-tool"></a>`enable-ask-permission-tool` | `false` |
| <a id="enable-ask-question-tool"></a>`enable-ask-question-tool` | `true` |
| <a id="enable-autonomous-mode-critic"></a>`enable-autonomous-mode-critic` | `false` |
| <a id="enable-background-task-accumulator"></a>`enable-background-task-accumulator` | `false` |
| <a id="enable-battle-contrib-variants"></a>`enable-battle-contrib-variants` | `false` |
| <a id="enable-bottom-terminal-panel"></a>`enable-bottom-terminal-panel` | `false` |
| <a id="enable-browser-site-data-controls"></a>`enable-browser-site-data-controls` | `false` |
| <a id="enable-browser-subagent-inline-pdf"></a>`enable-browser-subagent-inline-pdf` | `false` |
| <a id="enable-browser-subagent-url-binding"></a>`enable-browser-subagent-url-binding` | `false` |
| <a id="enable-cancel-before-send"></a>`enable-cancel-before-send` | `false` |
| <a id="enable-chatski-connect-page"></a>`enable-chatski-connect-page` | `false` |
| <a id="enable-code-search-palette"></a>`enable-code-search-palette` | `false` |
| <a id="enable-composer-connectors"></a>`enable-composer-connectors` | `false` |
| <a id="enable-concierge-agent"></a>`enable-concierge-agent` | `false` |
| <a id="enable-context-usage-indicator"></a>`enable-context-usage-indicator` | `false` |
| <a id="enable-conversation-forking"></a>`enable-conversation-forking` | `false` |
| <a id="enable-conversation-project-migration"></a>`enable-conversation-project-migration` | `true` |
| <a id="enable-conversation-sharing-prototype"></a>`enable-conversation-sharing-prototype` | `false` |
| <a id="enable-critique-decorator"></a>`enable-critique-decorator` | `false` |
| <a id="enable-critique-preview"></a>`enable-critique-preview` | `false` |
| <a id="enable-customization-kind-flow"></a>`enable-customization-kind-flow` | `false` |
| <a id="enable-customization-skills"></a>`enable-customization-skills` | `true` |
| <a id="enable-daemon-commands"></a>`enable-daemon-commands` | `false` |
| <a id="enable-deferred-tool-loading"></a>`enable-deferred-tool-loading` | `false` |
| <a id="enable-fast-output-close"></a>`enable-fast-output-close` | `true` |
| <a id="enable-file-diff-accumulator"></a>`enable-file-diff-accumulator` | `true` |
| <a id="enable-file-viewer-keyboard-navigation"></a>`enable-file-viewer-keyboard-navigation` | `false` |
| <a id="enable-fork-at-historical-step"></a>`enable-fork-at-historical-step` | `false` |
| <a id="enable-fork-in-new-abfs-workspace"></a>`enable-fork-in-new-abfs-workspace` | `false` |
| <a id="enable-fork-in-new-citc-workspace"></a>`enable-fork-in-new-citc-workspace` | `false` |
| <a id="enable-fork-in-new-worktree"></a>`enable-fork-in-new-worktree` | `false` |
| <a id="enable-ge-baic-quota-visibility"></a>`enable-ge-baic-quota-visibility` | `false` |
| <a id="enable-gemini-next-opt-out-switch"></a>`enable-gemini-next-opt-out-switch` | `false` |
| <a id="enable-get-screenshot-data"></a>`enable-get-screenshot-data` | `false` |
| <a id="enable-gil-logging"></a>`enable-gil-logging` | `false` |
| <a id="enable-ide-audio-transcription"></a>`enable-ide-audio-transcription` | `false` |
| <a id="enable-input-draft-persistence"></a>`enable-input-draft-persistence` | `false` |
| <a id="enable-jetbox-gcert"></a>`enable-jetbox-gcert` | `false` |
| <a id="enable-jetbox-gcert-v2"></a>`enable-jetbox-gcert-v2` | `false` |
| <a id="enable-jetski-chat"></a>`enable-jetski-chat` | `false` |
| <a id="enable-jetski-web-coordinators"></a>`enable-jetski-web-coordinators` | `false` |
| <a id="enable-jj-vcs-ui"></a>`enable-jj-vcs-ui` | `false` |
| <a id="enable-lazy-loaded-projects"></a>`enable-lazy-loaded-projects` | `false` |
| <a id="enable-learn-slash-command"></a>`enable-learn-slash-command` | `true` |
| <a id="enable-live-browser-aux-pane"></a>`enable-live-browser-aux-pane` | `false` |
| <a id="enable-markdown-agents"></a>`enable-markdown-agents` | `true` |
| <a id="enable-marketplace-new-badge"></a>`enable-marketplace-new-badge` | `false` |
| <a id="enable-move-conversation-to-project"></a>`enable-move-conversation-to-project` | `false` |
| <a id="enable-omni-mention-search"></a>`enable-omni-mention-search` | `false` |
| <a id="enable-open-with"></a>`enable-open-with` | `false` |
| <a id="enable-pane-selection-actions"></a>`enable-pane-selection-actions` | `false` |
| <a id="enable-paste-text-as-snippet"></a>`enable-paste-text-as-snippet` | `false` |
| <a id="enable-plugin-bundles"></a>`enable-plugin-bundles` | `false` |
| <a id="enable-plugin-themes"></a>`enable-plugin-themes` | `false` |
| <a id="enable-plugin-updates-menu"></a>`enable-plugin-updates-menu` | `false` |
| <a id="enable-profiles"></a>`enable-profiles` | `false` |
| <a id="enable-remote-battle-mode"></a>`enable-remote-battle-mode` | `false` |
| <a id="enable-remote-control-audio-transcription"></a>`enable-remote-control-audio-transcription` | `true` |
| <a id="enable-remote-conversation-live-browser"></a>`enable-remote-conversation-live-browser` | `false` |
| <a id="enable-remote-conversation-navigator"></a>`enable-remote-conversation-navigator` | `false` |
| <a id="enable-remote-conversation-preview"></a>`enable-remote-conversation-preview` | `false` |
| <a id="enable-remote-conversation-side-question"></a>`enable-remote-conversation-side-question` | `false` |
| <a id="enable-remote-conversation-terminal"></a>`enable-remote-conversation-terminal` | `false` |
| <a id="enable-remote-conversation-vm"></a>`enable-remote-conversation-vm` | `false` |
| <a id="enable-remote-coordinators"></a>`enable-remote-coordinators` | `false` |
| <a id="enable-remote-jj-commit-graph"></a>`enable-remote-jj-commit-graph` | `false` |
| <a id="enable-remote-persistent-agents"></a>`enable-remote-persistent-agents` | `false` |
| <a id="enable-remote-personal-agents"></a>`enable-remote-personal-agents` | `false` |
| <a id="enable-remote-standalone-conversations"></a>`enable-remote-standalone-conversations` | `false` |
| <a id="enable-remote-team-agents"></a>`enable-remote-team-agents` | `false` |
| <a id="enable-resource-manager"></a>`enable-resource-manager` | `false` |
| <a id="enable-run-command-semantic-rendering"></a>`enable-run-command-semantic-rendering` | `false` |
| <a id="enable-run-workflow-terminal-return"></a>`enable-run-workflow-terminal-return` | `false` |
| <a id="enable-send-immediately-by-default"></a>`enable-send-immediately-by-default` | `false` |
| <a id="enable-settings-search"></a>`enable-settings-search` | `false` |
| <a id="enable-sidebar-inbox-mode"></a>`enable-sidebar-inbox-mode` | `false` |
| <a id="enable-sidebar-redesign"></a>`enable-sidebar-redesign` | `false` |
| <a id="enable-sidecar-pill-auto-open"></a>`enable-sidecar-pill-auto-open` | `false` |
| <a id="enable-sidecars-user-config-migration-ready"></a>`enable-sidecars-user-config-migration-ready` | `false` |
| <a id="enable-simplified-skin"></a>`enable-simplified-skin` | `false` |
| <a id="enable-skill-accumulator"></a>`enable-skill-accumulator` | `false` |
| <a id="enable-skill-icons"></a>`enable-skill-icons` | `false` |
| <a id="enable-sqlite-trajectory"></a>`enable-sqlite-trajectory` | `true` |
| <a id="enable-state-accumulator"></a>`enable-state-accumulator` | `false` |
| <a id="enable-steer-framing"></a>`enable-steer-framing` | `false` |
| <a id="enable-styles-css-cleanup"></a>`enable-styles-css-cleanup` | `false` |
| <a id="enable-tasks"></a>`enable-tasks` | `true` |
| <a id="enable-terminal-setup-script"></a>`enable-terminal-setup-script` | `false` |
| <a id="enable-text-transitions"></a>`enable-text-transitions` | `false` |
| <a id="enable-thought-editing"></a>`enable-thought-editing` | `false` |
| <a id="enable-thought-steering-from-selection"></a>`enable-thought-steering-from-selection` | `false` |
| <a id="enable-thumbs-down-reroll"></a>`enable-thumbs-down-reroll` | `false` |
| <a id="enable-trajectory-debug-in-battle-mode"></a>`enable-trajectory-debug-in-battle-mode` | `false` |
| <a id="enable-unified-approved-sites"></a>`enable-unified-approved-sites` | `false` |
| <a id="enable-upload-html-artifact-to-drive"></a>`enable-upload-html-artifact-to-drive` | `false` |
| <a id="enable-versioned-plugin-dirs"></a>`enable-versioned-plugin-dirs` | `false` |
| <a id="enable-voice-remote-control"></a>`enable-voice-remote-control` | `false` |
| <a id="enable-wait-tool"></a>`enable-wait-tool` | `false` |
| <a id="enable-wcv-three-layer-stack"></a>`enable-wcv-three-layer-stack` | `false` |
| <a id="full_frame_memory_explorer"></a>`full_frame_memory_explorer` | `false` |
| <a id="google-workspace-mentions"></a>`google-workspace-mentions` | `false` |
| <a id="google3-default-org-policy"></a>`google3-default-org-policy` | `` |
| <a id="jetbox-usage-tips"></a>`jetbox-usage-tips` | `{}` |
| <a id="jetski-bundle-promotions"></a>`jetski-bundle-promotions` | `` |
| <a id="jetski-featured-plugins"></a>`jetski-featured-plugins` | `` |
| <a id="jetski-hiring-link-text"></a>`jetski-hiring-link-text` | `` |
| <a id="jetski-hiring-link-url"></a>`jetski-hiring-link-url` | `` |
| <a id="jetski-hub-announcement-message"></a>`jetski-hub-announcement-message` | `Please update Jetski to v2026.10.14.04+. If the Update pill …` |
| <a id="jetski-plugin-promotions"></a>`jetski-plugin-promotions` | `` |
| <a id="jetski-plugin-shelves"></a>`jetski-plugin-shelves` | `` |
| <a id="json-hooks-enabled"></a>`json-hooks-enabled` | `true` |
| <a id="markdown-virtualize-long-documents"></a>`markdown-virtualize-long-documents` | `false` |
| <a id="max-conversation-save-count"></a>`max-conversation-save-count` | `500` |
| <a id="max_injected_memories"></a>`max_injected_memories` | `-1` |
| <a id="max_injected_skills"></a>`max_injected_skills` | `-1` |
| <a id="mcp-lazy-load-tools"></a>`mcp-lazy-load-tools` | `true` |
| <a id="memory_operations_disabled_surfaces"></a>`memory_operations_disabled_surfaces` | `none` |
| <a id="memory_source_attribution"></a>`memory_source_attribution` | `false` |
| <a id="memory_synthesis_mode"></a>`memory_synthesis_mode` | `inline` |
| <a id="shadow-call-aicode-models"></a>`shadow-call-aicode-models` | `false` |
| <a id="show-all-models-in-gemini-app"></a>`show-all-models-in-gemini-app` | `false` |
| <a id="show-trajectory-labeling-ad"></a>`show-trajectory-labeling-ad` | `false` |
| <a id="show_memory_operation_diffs"></a>`show_memory_operation_diffs` | `false` |
| <a id="show_memory_operations"></a>`show_memory_operations` | `true` |
| <a id="si_dreaming_split_large_memories"></a>`si_dreaming_split_large_memories` | `false` |
| <a id="sonar-active-tags"></a>`sonar-active-tags` | `` |
| <a id="sso-based-user-identity"></a>`sso-based-user-identity` | `false` |
| <a id="turn-on-projects-internally"></a>`turn-on-projects-internally` | `true` |
| <a id="use-canonical-stub-for-checkin"></a>`use-canonical-stub-for-checkin` | `false` |
| <a id="use-model-reported-token-count"></a>`use-model-reported-token-count` | `true` |
| <a id="use_slope_gated_skills"></a>`use_slope_gated_skills` | `false` |
| <a id="verbose-errors"></a>`verbose-errors` | `false` |
| <a id="verifiers_experiment_config"></a>`verifiers_experiment_config` | `` |
| <a id="windows-titlebar-redesign"></a>`windows-titlebar-redesign` | `false` |

## Not in `agy`: other Google products

Switches named `Product__name` after other Google products, such as `DuetAi…`, `Chat__`, `GeminiCLI…` and `Datacloud…`. None appears in the `agy` 1.5.14 binary.

| Switch | Server value |
|---|---|
| <a id="ApigeeCloudCode__enable_mock_server"></a>`ApigeeCloudCode__enable_mock_server` | `true` |
| <a id="ApigeeGeminiChatTool__enable_apigee_gemini_chat_tool"></a>`ApigeeGeminiChatTool__enable_apigee_gemini_chat_tool` | `true` |
| <a id="Chat__chat_context_window_size"></a>`Chat__chat_context_window_size` | `-1` |
| <a id="Chat__code_customization_enable_learn_more_message"></a>`Chat__code_customization_enable_learn_more_message` | `true` |
| <a id="Chat__delete_chat_request_button"></a>`Chat__delete_chat_request_button` | `true` |
| <a id="Chat__delete_response_after_stop"></a>`Chat__delete_response_after_stop` | `true` |
| <a id="Chat__display_prompt_recitations"></a>`Chat__display_prompt_recitations` | `true` |
| <a id="Chat__edit_chat_request_button"></a>`Chat__edit_chat_request_button` | `true` |
| <a id="Chat__enable_agent_mode_slash_commands"></a>`Chat__enable_agent_mode_slash_commands` | `true` |
| <a id="Chat__enable_agentic_chat_ij"></a>`Chat__enable_agentic_chat_ij` | `true` |
| <a id="Chat__enable_async_chat_intent_classification"></a>`Chat__enable_async_chat_intent_classification` | `false` |
| <a id="Chat__enable_chat_agentic_mcp_chat"></a>`Chat__enable_chat_agentic_mcp_chat` | `false` |
| <a id="Chat__enable_chat_checkpoints"></a>`Chat__enable_chat_checkpoints` | `true` |
| <a id="Chat__enable_chat_crescendo_agents"></a>`Chat__enable_chat_crescendo_agents` | `true` |
| <a id="Chat__enable_chat_folder_context_selection"></a>`Chat__enable_chat_folder_context_selection` | `false` |
| <a id="Chat__enable_chat_gemini_cli"></a>`Chat__enable_chat_gemini_cli` | `true` |
| <a id="Chat__enable_chat_intent_classification"></a>`Chat__enable_chat_intent_classification` | `false` |
| <a id="Chat__enable_chat_moa"></a>`Chat__enable_chat_moa` | `false` |
| <a id="Chat__enable_chat_named_entity_recognition"></a>`Chat__enable_chat_named_entity_recognition` | `false` |
| <a id="Chat__enable_chat_rag_remote_repositories_context_selection"></a>`Chat__enable_chat_rag_remote_repositories_context_selection` | `true` |
| <a id="Chat__enable_chat_streaming"></a>`Chat__enable_chat_streaming` | `true` |
| <a id="Chat__enable_code_customization_webview"></a>`Chat__enable_code_customization_webview` | `true` |
| <a id="Chat__enable_custom_prompts"></a>`Chat__enable_custom_prompts` | `true` |
| <a id="Chat__enable_full_codebase_awareness_chat"></a>`Chat__enable_full_codebase_awareness_chat` | `true` |
| <a id="Chat__enable_gemini3_announcement"></a>`Chat__enable_gemini3_announcement` | `true` |
| <a id="Chat__enable_local_codebase_awareness_chat_ij"></a>`Chat__enable_local_codebase_awareness_chat_ij` | `true` |
| <a id="Chat__enable_mcp_server"></a>`Chat__enable_mcp_server` | `false` |
| <a id="Chat__enable_mcp_server_ij"></a>`Chat__enable_mcp_server_ij` | `true` |
| <a id="Chat__enable_sessions"></a>`Chat__enable_sessions` | `true` |
| <a id="Chat__enable_stream_generate_content_ij"></a>`Chat__enable_stream_generate_content_ij` | `false` |
| <a id="Chat__enable_suggested_prompts"></a>`Chat__enable_suggested_prompts` | `false` |
| <a id="Chat__enable_text_snippets"></a>`Chat__enable_text_snippets` | `true` |
| <a id="Chat__enable_workspace_change_in_chat_history"></a>`Chat__enable_workspace_change_in_chat_history` | `true` |
| <a id="Chat__fca_chat_context_window_size"></a>`Chat__fca_chat_context_window_size` | `3500000` |
| <a id="Chat__lca_chat_context_window_size_ij"></a>`Chat__lca_chat_context_window_size_ij` | `3500000` |
| <a id="Chat__local_bm25_chat_max_results"></a>`Chat__local_bm25_chat_max_results` | `10` |
| <a id="Chat__local_bm25_chat_tokenizer"></a>`Chat__local_bm25_chat_tokenizer` | `wald_word3` |
| <a id="Chat__local_bm25_index_max_files"></a>`Chat__local_bm25_index_max_files` | `25000` |
| <a id="Chat__stop_chat_request_button"></a>`Chat__stop_chat_request_button` | `true` |
| <a id="CliComplexityBasedRouting__enabled"></a>`CliComplexityBasedRouting__enabled` | `true` |
| <a id="CliComplexityBasedRouting__prompt_complexity"></a>`CliComplexityBasedRouting__prompt_complexity` | `90` |
| <a id="CodeassistMetrics__enable_codeassist_metric"></a>`CodeassistMetrics__enable_codeassist_metric` | `false` |
| <a id="DatacloudBigqueryGraph__enable_bigquery_graph"></a>`DatacloudBigqueryGraph__enable_bigquery_graph` | `true` |
| <a id="DatacloudDatabases__discoverability_improvements"></a>`DatacloudDatabases__discoverability_improvements` | `true` |
| <a id="DatacloudDatabases__enable_cloud_sql_built_in_user"></a>`DatacloudDatabases__enable_cloud_sql_built_in_user` | `true` |
| <a id="DatacloudDatabases__enable_data_preview"></a>`DatacloudDatabases__enable_data_preview` | `true` |
| <a id="DatacloudDatabases__enable_query_execution_details"></a>`DatacloudDatabases__enable_query_execution_details` | `true` |
| <a id="DatacloudDatabases__enable_query_settings_improvements"></a>`DatacloudDatabases__enable_query_settings_improvements` | `true` |
| <a id="DatacloudDataplex__enable_alloydb_adapter"></a>`DatacloudDataplex__enable_alloydb_adapter` | `true` |
| <a id="DatacloudDataplex__enable_biglake_adapter"></a>`DatacloudDataplex__enable_biglake_adapter` | `true` |
| <a id="DatacloudDataplex__enable_bigquery_adapter"></a>`DatacloudDataplex__enable_bigquery_adapter` | `true` |
| <a id="DatacloudDataplex__enable_bigtable_adapter"></a>`DatacloudDataplex__enable_bigtable_adapter` | `true` |
| <a id="DatacloudDataplex__enable_cloud_sql_adapter"></a>`DatacloudDataplex__enable_cloud_sql_adapter` | `true` |
| <a id="DatacloudDataplex__enable_firestore_adapter"></a>`DatacloudDataplex__enable_firestore_adapter` | `true` |
| <a id="DatacloudDataplex__enable_lakehouse_public_data"></a>`DatacloudDataplex__enable_lakehouse_public_data` | `true` |
| <a id="DatacloudDataplex__enable_spanner_adapter"></a>`DatacloudDataplex__enable_spanner_adapter` | `true` |
| <a id="DatacloudFeedback__enable_listnr_survey"></a>`DatacloudFeedback__enable_listnr_survey` | `true` |
| <a id="DatacloudKnowledgeCatalog__enable_data_products"></a>`DatacloudKnowledgeCatalog__enable_data_products` | `true` |
| <a id="DatacloudKnowledgeCatalog__enable_post_next_improvements"></a>`DatacloudKnowledgeCatalog__enable_post_next_improvements` | `true` |
| <a id="DatacloudKnowledgeCatalog__enable_relationship_graph_details_panel"></a>`DatacloudKnowledgeCatalog__enable_relationship_graph_details_panel` | `true` |
| <a id="DatacloudMcp__enable_stdio_mcp_proxy"></a>`DatacloudMcp__enable_stdio_mcp_proxy` | `true` |
| <a id="DatacloudSpark__enable_jupyter_token_broker"></a>`DatacloudSpark__enable_jupyter_token_broker` | `true` |
| <a id="DatacloudSpark__enable_load_in_spark_dataframe_from_gcs"></a>`DatacloudSpark__enable_load_in_spark_dataframe_from_gcs` | `true` |
| <a id="DatacloudSpark__enable_runtime_profile"></a>`DatacloudSpark__enable_runtime_profile` | `true` |
| <a id="DatacloudSpark__enable_spark_in_sql_editor"></a>`DatacloudSpark__enable_spark_in_sql_editor` | `true` |
| <a id="DatacloudTheme__enable_theme_v2"></a>`DatacloudTheme__enable_theme_v2` | `false` |
| <a id="DuetAICodeTransformIj__enable_ij"></a>`DuetAICodeTransformIj__enable_ij` | `true` |
| <a id="DuetAICodeTransform__custom_slash_commands"></a>`DuetAICodeTransform__custom_slash_commands` | `true` |
| <a id="DuetAICodeTransform__display_prompt_recitations"></a>`DuetAICodeTransform__display_prompt_recitations` | `true` |
| <a id="DuetAICodeTransform__enable_inline_diff"></a>`DuetAICodeTransform__enable_inline_diff` | `true` |
| <a id="DuetAICodeTransform__enable_m2"></a>`DuetAICodeTransform__enable_m2` | `true` |
| <a id="DuetAiCloudCodeAPI__enable_cloudcode_api"></a>`DuetAiCloudCodeAPI__enable_cloudcode_api` | `true` |
| <a id="DuetAiCompletion__adaptingCache_maxInflightRequests"></a>`DuetAiCompletion__adaptingCache_maxInflightRequests` | `2` |
| <a id="DuetAiCompletion__codeCompletion_client_side_context_size_limit"></a>`DuetAiCompletion__codeCompletion_client_side_context_size_limit` | `35000` |
| <a id="DuetAiCompletion__codeCompletion_enableAdaptingCache"></a>`DuetAiCompletion__codeCompletion_enableAdaptingCache` | `false` |
| <a id="DuetAiCompletion__codeCompletion_enableInfixCache"></a>`DuetAiCompletion__codeCompletion_enableInfixCache` | `false` |
| <a id="DuetAiCompletion__codeCompletion_enablePrefetchNextSuggestions"></a>`DuetAiCompletion__codeCompletion_enablePrefetchNextSuggestions` | `true` |
| <a id="DuetAiCompletion__codeCompletion_triggerForDeletion"></a>`DuetAiCompletion__codeCompletion_triggerForDeletion` | `false` |
| <a id="DuetAiGenerationAndCompletion__track_suffix_length"></a>`DuetAiGenerationAndCompletion__track_suffix_length` | `false` |
| <a id="DuetAiGeneration__auto_trigger_on_empty_class_struct_def"></a>`DuetAiGeneration__auto_trigger_on_empty_class_struct_def` | `true` |
| <a id="DuetAiGeneration__codeGeneration_context_window_size"></a>`DuetAiGeneration__codeGeneration_context_window_size` | `64000` |
| <a id="DuetAiGeneration__codeGeneration_enable_codelens_call_to_action"></a>`DuetAiGeneration__codeGeneration_enable_codelens_call_to_action` | `false` |
| <a id="DuetAiGeneration__codeGeneration_enable_diff_view"></a>`DuetAiGeneration__codeGeneration_enable_diff_view` | `false` |
| <a id="DuetAiGeneration__codeGeneration_enable_new_cy_vsc_ux"></a>`DuetAiGeneration__codeGeneration_enable_new_cy_vsc_ux` | `false` |
| <a id="DuetAiGeneration__codeGeneration_enable_pane_view"></a>`DuetAiGeneration__codeGeneration_enable_pane_view` | `false` |
| <a id="DuetAiGeneration__codeGeneration_enable_quickpick_chat"></a>`DuetAiGeneration__codeGeneration_enable_quickpick_chat` | `true` |
| <a id="DuetAiGeneration__codeGeneration_pane_view_default_config"></a>`DuetAiGeneration__codeGeneration_pane_view_default_config` | `false` |
| <a id="DuetAiGeneration__codeGeneration_use_transform_api"></a>`DuetAiGeneration__codeGeneration_use_transform_api` | `false` |
| <a id="DuetAiLocalRag__bm25_in_completion_max_results"></a>`DuetAiLocalRag__bm25_in_completion_max_results` | `15` |
| <a id="DuetAiLocalRag__cache_co_located"></a>`DuetAiLocalRag__cache_co_located` | `20` |
| <a id="DuetAiLocalRag__cache_file_limit"></a>`DuetAiLocalRag__cache_file_limit` | `4194300` |
| <a id="DuetAiLocalRag__cache_total_files"></a>`DuetAiLocalRag__cache_total_files` | `250` |
| <a id="DuetAiLocalRag__enable_local_rag"></a>`DuetAiLocalRag__enable_local_rag` | `true` |
| <a id="DuetAiLocalRag__enable_local_rag_chat"></a>`DuetAiLocalRag__enable_local_rag_chat` | `true` |
| <a id="DuetAiLocalRag__enable_local_rag_completion"></a>`DuetAiLocalRag__enable_local_rag_completion` | `true` |
| <a id="DuetAiLocalRag__enable_local_rag_completion_snippets"></a>`DuetAiLocalRag__enable_local_rag_completion_snippets` | `false` |
| <a id="DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning"></a>`DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning` | `false` |
| <a id="DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning_bm25_scoring"></a>`DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning_bm25_scoring` | `false` |
| <a id="DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning_colocated_files"></a>`DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning_colocated_files` | `false` |
| <a id="DuetAiLocalRag__enable_wald_file_selection"></a>`DuetAiLocalRag__enable_wald_file_selection` | `false` |
| <a id="DuetAiLocalRag__include_currently_open_files"></a>`DuetAiLocalRag__include_currently_open_files` | `true` |
| <a id="DuetAiLocalRag__include_doc_files"></a>`DuetAiLocalRag__include_doc_files` | `false` |
| <a id="DuetAiLocalRag__include_unit_test_files"></a>`DuetAiLocalRag__include_unit_test_files` | `false` |
| <a id="DuetAiLocalRag__local_rag_fragmentation_algorithm"></a>`DuetAiLocalRag__local_rag_fragmentation_algorithm` | `wholefile` |
| <a id="DuetAiLocalRag__local_rag_reranking_by_language"></a>`DuetAiLocalRag__local_rag_reranking_by_language` | `0` |
| <a id="DuetAiLocalRag__local_rag_tokenization_algorithm"></a>`DuetAiLocalRag__local_rag_tokenization_algorithm` | `whitespace` |
| <a id="DuetAiLocalRag__max_file_search_depth"></a>`DuetAiLocalRag__max_file_search_depth` | `2` |
| <a id="DuetAiLocalRag__merge_fragments"></a>`DuetAiLocalRag__merge_fragments` | `true` |
| <a id="DuetAiLocalRag__otherfiles_chat_limit"></a>`DuetAiLocalRag__otherfiles_chat_limit` | `100` |
| <a id="DuetAiLocalRag__otherfiles_completion_limit"></a>`DuetAiLocalRag__otherfiles_completion_limit` | `15` |
| <a id="DuetAiLocalRag__otherfiles_generation_limit"></a>`DuetAiLocalRag__otherfiles_generation_limit` | `40` |
| <a id="DuetAiLocalRag__sliding_window_fragmenter_stride"></a>`DuetAiLocalRag__sliding_window_fragmenter_stride` | `10` |
| <a id="DuetAiLocalRag__sliding_window_fragmenter_window_size"></a>`DuetAiLocalRag__sliding_window_fragmenter_window_size` | `20` |
| <a id="DuetAiLocalRag__substrings_to_identify_doc_prompts"></a>`DuetAiLocalRag__substrings_to_identify_doc_prompts` | `{"values": ["document", "comment"]}` |
| <a id="DuetAiLocalRag__substrings_to_identify_test_prompts"></a>`DuetAiLocalRag__substrings_to_identify_test_prompts` | `{"values": ["test"]}` |
| <a id="DuetAiLocalRag__top_k_doc_files_to_include"></a>`DuetAiLocalRag__top_k_doc_files_to_include` | `2` |
| <a id="DuetAiLocalRag__top_k_test_files_to_include"></a>`DuetAiLocalRag__top_k_test_files_to_include` | `2` |
| <a id="DuetAiLocalRag__wald_local_rag_max_file_search_depth"></a>`DuetAiLocalRag__wald_local_rag_max_file_search_depth` | `-1` |
| <a id="DuetAiMendelOverrides__chat_clientId"></a>`DuetAiMendelOverrides__chat_clientId` | `CHAT_CLIENT_CLOUD_CODE_GEMINI_2_0_FLASH_001` |
| <a id="DuetAiMendelOverrides__enable_gca_intent_classification_as_default_model"></a>`DuetAiMendelOverrides__enable_gca_intent_classification_as_default_model` | `false` |
| <a id="DuetAiMendelOverrides__enable_gca_intent_classification_model_for_logging"></a>`DuetAiMendelOverrides__enable_gca_intent_classification_model_for_logging` | `false` |
| <a id="DuetAiMendelOverrides__gca_intent_classifier_model_name"></a>`DuetAiMendelOverrides__gca_intent_classifier_model_name` | `/ml/m2p-role-prod-intentclassifiergca-servo-owner/prod.inten…` |
| <a id="DuetAiMendelOverrides__gca_intent_classifier_model_version"></a>`DuetAiMendelOverrides__gca_intent_classifier_model_version` | `` |
| <a id="DuetAiMendelOverrides__inlineSuggestions_debounceMs"></a>`DuetAiMendelOverrides__inlineSuggestions_debounceMs` | `300` |
| <a id="DuetAiMendelOverrides__inlineSuggestions_debounced_after_fetching"></a>`DuetAiMendelOverrides__inlineSuggestions_debounced_after_fetching` | `false` |
| <a id="DuetAiMendelOverrides__inlineSuggestions_throttleMs"></a>`DuetAiMendelOverrides__inlineSuggestions_throttleMs` | `0` |
| <a id="DuetAiProcessors__enable_prompt_recitations_check"></a>`DuetAiProcessors__enable_prompt_recitations_check` | `true` |
| <a id="DuetAiRemoteRag__enable_hyde_for_generation"></a>`DuetAiRemoteRag__enable_hyde_for_generation` | `false` |
| <a id="DuetAiRemoteRag__enable_remote_rag"></a>`DuetAiRemoteRag__enable_remote_rag` | `true` |
| <a id="DuetAiRemoteRag__enable_remote_rag_chat"></a>`DuetAiRemoteRag__enable_remote_rag_chat` | `true` |
| <a id="DuetAiRemoteRag__max_bm25_snippets_rag"></a>`DuetAiRemoteRag__max_bm25_snippets_rag` | `0` |
| <a id="DuetAiRemoteRag__max_distance_rag_for_chat"></a>`DuetAiRemoteRag__max_distance_rag_for_chat` | `0.8` |
| <a id="DuetAiRemoteRag__max_distance_rag_for_selected_code"></a>`DuetAiRemoteRag__max_distance_rag_for_selected_code` | `0.8` |
| <a id="DuetAiRemoteRag__max_distance_tailed_prompt"></a>`DuetAiRemoteRag__max_distance_tailed_prompt` | `0.4` |
| <a id="DuetAiRemoteRag__max_named_entity_for_chat"></a>`DuetAiRemoteRag__max_named_entity_for_chat` | `3` |
| <a id="DuetAiRemoteRag__max_snippets_rag_for_chat"></a>`DuetAiRemoteRag__max_snippets_rag_for_chat` | `2` |
| <a id="DuetAiRemoteRag__max_snippets_rag_for_selected_code"></a>`DuetAiRemoteRag__max_snippets_rag_for_selected_code` | `2` |
| <a id="DuetAiRemoteRag__max_snippets_tailed_prompt"></a>`DuetAiRemoteRag__max_snippets_tailed_prompt` | `4` |
| <a id="DuetAiRemoteRag__multi_query_tail_ns_for_completion"></a>`DuetAiRemoteRag__multi_query_tail_ns_for_completion` | `{}` |
| <a id="DuetAiRemoteRag__multi_query_tail_ns_for_generation"></a>`DuetAiRemoteRag__multi_query_tail_ns_for_generation` | `{}` |
| <a id="DuetAi__custom_preambles"></a>`DuetAi__custom_preambles` | `true` |
| <a id="FirebaseDataConnectChatTool__enable_firebase_data_connect_chat_tool"></a>`FirebaseDataConnectChatTool__enable_firebase_data_connect_chat_tool` | `false` |
| <a id="GCAFeedbackBlock__enable_feedback_block"></a>`GCAFeedbackBlock__enable_feedback_block` | `false` |
| <a id="GCAUpgradeToPaid__current_tier_polling_interval_ms"></a>`GCAUpgradeToPaid__current_tier_polling_interval_ms` | `43200000` |
| <a id="GCAUpgradeToPaid__enable_g1_upgrade_flow"></a>`GCAUpgradeToPaid__enable_g1_upgrade_flow` | `false` |
| <a id="GCAUpgradeToPaid__enable_upgrade_from_free_tier"></a>`GCAUpgradeToPaid__enable_upgrade_from_free_tier` | `true` |
| <a id="GcaAipluginSwingToCompose__enable_compose"></a>`GcaAipluginSwingToCompose__enable_compose` | `false` |
| <a id="GcaApiMigration__platform_api_chat_experience"></a>`GcaApiMigration__platform_api_chat_experience` | `` |
| <a id="GcaApiMigration__platform_api_completion_experience"></a>`GcaApiMigration__platform_api_completion_experience` | `` |
| <a id="GcaApiMigration__platform_api_generation_experience"></a>`GcaApiMigration__platform_api_generation_experience` | `` |
| <a id="GcaApiMigration__platform_api_transformation_experience"></a>`GcaApiMigration__platform_api_transformation_experience` | `` |
| <a id="GcaApiMigration__product_api_chat_experience"></a>`GcaApiMigration__product_api_chat_experience` | `` |
| <a id="GcaApiMigration__product_api_completion_experience"></a>`GcaApiMigration__product_api_completion_experience` | `` |
| <a id="GcaApiMigration__product_api_generation_experience"></a>`GcaApiMigration__product_api_generation_experience` | `` |
| <a id="GcaApiMigration__product_api_transformation_experience"></a>`GcaApiMigration__product_api_transformation_experience` | `` |
| <a id="GcaCitationBlock__enable_citation_block"></a>`GcaCitationBlock__enable_citation_block` | `false` |
| <a id="GcaEventsPipeline__enable_events_pipeline_polling"></a>`GcaEventsPipeline__enable_events_pipeline_polling` | `false` |
| <a id="GcaEventsPipeline__events_pipeline_polling_interval_ms"></a>`GcaEventsPipeline__events_pipeline_polling_interval_ms` | `60000` |
| <a id="GcaFlashCompletions__completion_replaces_cursor_line"></a>`GcaFlashCompletions__completion_replaces_cursor_line` | `false` |
| <a id="GcaFlashCompletions__enable_client_postprocessing"></a>`GcaFlashCompletions__enable_client_postprocessing` | `false` |
| <a id="GcaFlashCompletions__enable_flash_completions"></a>`GcaFlashCompletions__enable_flash_completions` | `false` |
| <a id="GcaTelemetryBlock__enable_telemetry_block"></a>`GcaTelemetryBlock__enable_telemetry_block` | `false` |
| <a id="GcaTelemetry__enable_ai_characters_percentage"></a>`GcaTelemetry__enable_ai_characters_percentage` | `true` |
| <a id="GcaTelemetry__send_aica_to_ccpa"></a>`GcaTelemetry__send_aica_to_ccpa` | `true` |
| <a id="GcaTransformFinishChanges__enable_finish_changes"></a>`GcaTransformFinishChanges__enable_finish_changes` | `false` |
| <a id="GcaTransformOutlines__enable_automatic_generation"></a>`GcaTransformOutlines__enable_automatic_generation` | `false` |
| <a id="GcaTransformOutlines__enable_outlines"></a>`GcaTransformOutlines__enable_outlines` | `true` |
| <a id="GcaUx__enable_gm3_design_system"></a>`GcaUx__enable_gm3_design_system` | `true` |
| <a id="GcliAgentHistoryTruncation__agent_history_retained_messages"></a>`GcliAgentHistoryTruncation__agent_history_retained_messages` | `15` |
| <a id="GcliAgentHistoryTruncation__agent_history_truncation_threshold"></a>`GcliAgentHistoryTruncation__agent_history_truncation_threshold` | `30` |
| <a id="GcliAgentHistoryTruncation__enable_agent_history_truncation"></a>`GcliAgentHistoryTruncation__enable_agent_history_truncation` | `false` |
| <a id="GcliCompressionPrompt__compression_prompt"></a>`GcliCompressionPrompt__compression_prompt` | `` |
| <a id="GcliConfigPayload__config_payload"></a>`GcliConfigPayload__config_payload` | `` |
| <a id="GcliConfig__cli_max_attempts"></a>`GcliConfig__cli_max_attempts` | `10` |
| <a id="GeminiCLIBannerText__capacity_issues"></a>`GeminiCLIBannerText__capacity_issues` | `` |
| <a id="GeminiCLIBannerText__no_capacity_issues"></a>`GeminiCLIBannerText__no_capacity_issues` | `` |
| <a id="GeminiCLIContextCompression__threshold_fraction"></a>`GeminiCLIContextCompression__threshold_fraction` | `0` |
| <a id="GeminiCLIIsLaunched__gemini_3_1_flash_lite_ga_launched"></a>`GeminiCLIIsLaunched__gemini_3_1_flash_lite_ga_launched` | `true` |
| <a id="GeminiCLIIsLaunched__gemini_3_1_flash_lite_preview_launched"></a>`GeminiCLIIsLaunched__gemini_3_1_flash_lite_preview_launched` | `true` |
| <a id="GeminiCLIIsLaunched__gemini_3_1_flash_preview_launched"></a>`GeminiCLIIsLaunched__gemini_3_1_flash_preview_launched` | `true` |
| <a id="GeminiCLIIsLaunched__gemini_3_1_pro_preview_launched"></a>`GeminiCLIIsLaunched__gemini_3_1_pro_preview_launched` | `true` |
| <a id="GeminiCLIIsLaunched__gemini_3_8_flash_launched"></a>`GeminiCLIIsLaunched__gemini_3_8_flash_launched` | `true` |
| <a id="GeminiCLIIsLaunched__gemini_3_flash_ga_launched"></a>`GeminiCLIIsLaunched__gemini_3_flash_ga_launched` | `true` |
| <a id="GeminiCLIIsLaunched__gemini_3_pro_launched"></a>`GeminiCLIIsLaunched__gemini_3_pro_launched` | `true` |
| <a id="GeminiCLIPreviewAvailable__enable_preview"></a>`GeminiCLIPreviewAvailable__enable_preview` | `true` |
| <a id="GeminiFreeTier__call_onboard_user_from_legacy_flow"></a>`GeminiFreeTier__call_onboard_user_from_legacy_flow` | `true` |
| <a id="GeminiFreeTier__enable_free_tier"></a>`GeminiFreeTier__enable_free_tier` | `false` |
| <a id="GeminiFreeTier__license_message_frequency_days"></a>`GeminiFreeTier__license_message_frequency_days` | `60` |
| <a id="IntentAware__ellipsis"></a>`IntentAware__ellipsis` | `false` |
| <a id="IntentAware__enable_intent_aware_m1"></a>`IntentAware__enable_intent_aware_m1` | `true` |
| <a id="MetricService__enable_metric_service"></a>`MetricService__enable_metric_service` | `true` |
| <a id="SDLCAgents__enable_anthropic_model_connection"></a>`SDLCAgents__enable_anthropic_model_connection` | `false` |
| <a id="SDLCAgents__enable_azure_model_connection"></a>`SDLCAgents__enable_azure_model_connection` | `false` |
| <a id="SDLCAgents__enable_gemini_model_connection"></a>`SDLCAgents__enable_gemini_model_connection` | `false` |
| <a id="SDLCAgents__enable_rest_model_connection"></a>`SDLCAgents__enable_rest_model_connection` | `false` |
| <a id="SparkMonitorIntegration__enable_spark_monitor_integration"></a>`SparkMonitorIntegration__enable_spark_monitor_integration` | `true` |
| <a id="SyntaxAnalysis__enable_syntax_analysis"></a>`SyntaxAnalysis__enable_syntax_analysis` | `false` |
| <a id="Timeouts__cli_request_timeout_seconds"></a>`Timeouts__cli_request_timeout_seconds` | `300` |
| <a id="Timeouts__cli_total_request_timeout_seconds"></a>`Timeouts__cli_total_request_timeout_seconds` | `600` |
| <a id="UserTelemetry__enable_user_telemetry_call"></a>`UserTelemetry__enable_user_telemetry_call` | `true` |

## Per-model switches

`fetchAvailableModels` also sends switches for each model. For that model they replace the global value of the same name. Full response: [available-models.json](available-models.json).

| Switch | Models that have it | What it does |
|---|---|---|
| <a id="CASCADE_USE_EXPERIMENT_CHECKPOINTER"></a>`CASCADE_USE_EXPERIMENT_CHECKPOINTER` | 35 | Compaction strategy, window limit and threshold for this model |
| <a id="CASCADE_USE_EXPERIMENT_CHECKPOINTER/claude-1m_840k"></a>`CASCADE_USE_EXPERIMENT_CHECKPOINTER/claude-1m_840k` | 6 |  |
| `allow-experimental-monitors` | 17 | Turns on Google DeepMind's review models for this model |
| <a id="cascade-include-ephemeral-message"></a>`cascade-include-ephemeral-message` | 21 |  |
| <a id="plan-execute-config"></a>`plan-execute-config` | 2 |  |
| <a id="retry_model_capacity_exhausted"></a>`retry_model_capacity_exhausted` | 33 |  |
| <a id="task-details-suffix"></a>`task-details-suffix` | 16 |  |
| <a id="template__system_prompts__guidelines"></a>`template__system_prompts__guidelines` | 7 | Replaces the prompt's `<guidelines>` section for this model |
