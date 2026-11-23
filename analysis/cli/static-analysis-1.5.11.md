# Antigravity CLI 1.5.11 — static privacy/security assessment

## Scope

Static analysis of the macOS arm64 `agy` 1.5.11 binary (226,097,264 bytes, SHA-256 `62f2102784bb74b7743ce31f45e8414f1111dc99efb395a9e62690af0336c677`) and `install.sh` (SHA-256 `62966c07365423bd4dc209355060744058fb30d60f5323e2d360e39de64e5042`). Addresses prefixed `0x10...` are unslid virtual addresses; `0x3...`/`0x4...` are file offsets. Compiled capability is not proof that a server flag enables it.

## Executive findings

| ID | Severity | Finding | Preconditions / qualification |
|---|---|---|---|
| CLI-01 | High | Background updates trust an unsigned manifest and its same-source SHA-512; no independent publisher-signature verification in the reviewed updater path | Release service/manifest compromise or trusted release-base override; not a demonstrated Google compromise |
| CLI-02 | Medium | OAuth credentials can fall back from Keychain to readable JSON files | Keyring unavailable, caller override, or environment detection; files are created with restrictive permissions, not world-readable by design |
| CLI-03 | Medium (privacy) | Telemetry infrastructure supports full source/prompt/tool-result/trajectory content, not merely counters | Content collection/redaction depends on policy, account and flags; compiled schema does not establish that every payload is uploaded |
| CLI-04 | Medium (deployment risk) | Tool approval and OS sandboxing are separate; explicit flags allow unattended tools and sandbox opt-in | Intentional capability, not a proven permission bypass; dangerous for untrusted prompts/repositories if deployed without safeguards |
| CLI-05 | Medium | Bootstrapper deliberately removes Gatekeeper quarantine and executes the newly downloaded CLI | Exact supplied script; checksum verifies same-manifest integrity but not independent publisher identity |
| CLI-06 | Low | Custom installation directory is interpolated into shell startup code without shell escaping | A crafted `--dir`/installation path must be supplied; no remote injection entry point established |
| CLI-07 | Low | Manual/pasted-URL OAuth path extracts `code` without validating pasted `state` | PKCE materially mitigates ordinary code injection; no working account takeover shown |
| CLI-08 | Info | Broad Google OAuth scopes and distributable native-app client secrets | Native client credentials are public identifiers, not server secrets; scopes matter more than secrecy of these values |

No Critical vulnerability or unauthenticated remote-code-execution exploit was established.

## 1. Package / architecture map

Recovered names use Google's internal `google3/third_party/jetski/` tree. This is not a thin launcher for the desktop language server: the CLI embeds the agent backend, model transports and Cortex machinery.

| Area | CLI-specific packages / symbols |
|---|---|
| Entrypoint/config | `main`, `cli/entrypoints/entrypoints`, `cli/env/env`, `cli/types/types`, `cli/store/store`, `cli/daemonsettings/daemonsettings` |
| Interactive TUI | `cli/model/model`, `cli/render/render`, `cli/layout/layout`, `cli/viewport/viewport`, `cli/termio/termio`, `cli/termstatus/termstatus`, `cli/keybindings/keybindings`, `cli/editing/*`, `cli/theme/*`, `cli/graphic/graphic`, `cli/live/live` |
| Headless / ACP | `cli/printmode/printmode`, `cli/headless/headless`, `cli/acp/{acp,jsonrpc,router,schema,server,session,sessionconfig,stepview,messages,mcp,auth}` |
| Authentication | `cli/backend/auth/auth`: `ChainedAuth`, `oauthMethod`, `keyringAuth`, `adcAuth`, `geminiAPIKeyAuth`, `gatewayAuth`, WIF, host auth; ACP has a separate `loopbackLogin` |
| Agent backend | `cli/backend/backend`, `cli/modelresolver/modelresolver`, `cli/project/project`, `cli/artifactreview/artifactreview`, `cli/steps/steps` |
| Remote / swarm | `cli/remotecontrol/remotecontrol`, `cli/swarm/{swarm,bee,live,tui}` |
| Customization | `cli/mcp/mcp`, `cli/plugins/{plugins,gemini,claude,marketplace,common}`, `cli/slashcmd/slashcmd`, `cli/commands/commands` |
| Telemetry | `cli/analytics/analytics`, `cli/perfrecorder/perfrecorder`, shared `devtools/jetski/telemetry/client/telemetryclient` and `language_server/telemetry/telemetry` |
| Installation/update | `cli/installer/installer`, `cli/updater/updater`, `cli/changelog/changelog` |
| Audio | `cli/audio/{audio,micperm,micserve,micwire,miniaudio}` |

`packages.txt` contains the recovered CLI package list. Only a limited shared-code comparison was made against the supplied language_server: strings for `cortex/`, `language_server/code_assist_client/codeassistclient` and `devtools/jetski/telemetry/client/telemetryclient` occur in both; the CLI-specific `cli/backend/auth/auth` string was absent in the LS comparison. Evidence: `globals-shared.txt`, with offsets in each binary. This is shared source-package evidence, not proof the binaries implement identical policy.

## 2. Login flow

### OAuth applications / endpoints

| Use | Client ID | Embedded client secret | Evidence |
|---|---|---|---|
| Consumer/Antigravity CLI | `1071006060591-tmhssin2h21lcre235vtolojh4g403ep.apps.googleusercontent.com` | `[oauth client secret]` | `auth.getOauthParams` @ `0x101d7b690`, ACP `newBrowserLogin` @ `0x10236ac10`; secret string file offset `0x3c829ef` |
| Business/GCP | `884354919052-36trc1jjb3tguiac32ov6cod268c5blh.apps.googleusercontent.com` | `[oauth client secret]` | `ChainedAuth.StartGCPAuthProcess` @ `0x101d7a4e0`, ACP `newBusinessBrowserLogin` @ `0x10236ad80` |
| Additional shared embedded application | `1098991175725-mdu1oe5cbbe1ckggsebobh94toiap657.apps.googleusercontent.com` | `[oauth client secret]` | File offsets `0x3d3a9de` and `0x3c835f7`; not established as the active CLI login application |

The third pairing is adjacent shared credential evidence, not proof that this application is selected by the CLI login flow. These native-app secrets are extractable by every recipient; their presence alone is not a credential-leak vulnerability.

Production Google endpoint constants decoded from global `0x10d56af40`:

- Authorization: `https://accounts.google.com/o/oauth2/auth`
- Token exchange/refresh: `https://oauth2.googleapis.com/token`
- Device endpoint also embedded: `https://oauth2.googleapis.com/device/code`; **no CLI device-code login branch was established**. Do not equate this library constant with a supported CLI device flow.
- User profile: `https://www.googleapis.com/oauth2/v2/userinfo`, loaded in `auth.fetchUserInfo` @ `0x101d7c740` and `fetchAdcUserInfo` @ `0x101d79e10`.

Base scope vector at `0x10d575588` is exactly:

1. `https://www.googleapis.com/auth/cloud-platform`
2. `https://www.googleapis.com/auth/userinfo.email`
3. `https://www.googleapis.com/auth/userinfo.profile`
4. `https://www.googleapis.com/auth/cclog`
5. `https://www.googleapis.com/auth/experimentsandconfigs`

Consumer scope builders additionally append `openid`, and have conditional inclusion/filtering logic for `https://www.googleapis.com/auth/aicode`. GCP uses the base vector. Evidence: `auth.getScopesByAuthMethod` @ `0x101d7b870`, ACP `consumerOAuthScopes` @ `0x10236a6a0`, and `globals_probe.py` output. The broad cloud-platform grant is materially broader than an app-specific inference scope; it does not itself bypass IAM authorization.

### A. Ordinary interactive CLI: browser + code/URL paste

Mermaid-ready sequence:

```mermaid
sequenceDiagram
  participant U as User
  participant C as CLI
  participant B as Browser
  participant G as Google OAuth
  participant H as antigravity.google callback
  participant S as Code Assist / AICode
  participant K as Keychain or token JSON
  C->>K: Load saved token / selected auth method
  alt Valid saved credentials
    C->>G: Refresh if needed
  else Interactive consumer or GCP OAuth
    C->>C: Generate PKCE verifier and S256 challenge; random state
    C->>B: Open authorization URL / show URL for remote session
    B->>G: OAuth consent with client ID, scopes, state, PKCE
    G->>H: Redirect with authorization code
    H-->>U: Authorization code / callback URL
    U->>C: Paste code or URL
    C->>C: Extract code; pasted state not checked in SubmitAuthorizationCode
    C->>G: Token exchange with code_verifier and client credentials
    C->>G: Fetch userinfo / identity claims
    C->>K: Store access/refresh token and account metadata
  end
  C->>S: LoadCodeAssist / license and user-tier resolution
  opt Account/project onboarding needed
    C->>S: OnboardUser / enterprise license/project operations
    C->>K: Persist project, region, user tier
  end
```

Evidence:

- `oauthMethod.StartInteractive` @ `0x101d7ad20` uses redirect URI **`https://antigravity.google/oauth-callback`**, not a loopback listener. It calls `oauth2.GenerateVerifier`, `auth.generateState`, `oauth2.S256ChallengeFromVerifier`, and `Config.AuthCodeURL`. SSH environment checks include `SSH_CLIENT`, `SSH_TTY`, `SSH_CONNECTION`, relevant to browser availability.
- `generateState` @ `0x101d7c210` calls `crypto/rand.Read` then Base64 encoding.
- `SubmitAuthorizationCode` @ `0x101d7b0f0` accepts a raw code or a string beginning `http://`/`https://`; URL parsing extracts only query key `code` then calls `completeOAuth`. There is no query-state extraction in this function. PKCE is still used in completion.
- `oauthMethod.completeOAuth.func1` and `savingTokenSource.Token` perform exchange/profile/persistence; `refreshAndSaveStoredToken` @ `0x101d7cc60` obtains a refreshing token source, saves the refreshed token and remembers ID-token claims.
- Login may select existing auth or ADC instead of browser OAuth: `RunLogin` @ `0x101d7ced0`; `ChainedAuth.Authenticate` @ `0x101d7a150`.

### B. ACP: separate loopback OAuth implementation

1. `loopbackLogin.acquire` @ `0x10236b4d0` calls `net.Listen("tcp", "localhost:0")`: ephemeral OS-selected port, not a fixed 4713 port.
2. Builds a loopback redirect using the selected listener port and callback route. The compiled callback route is `/auth/callback`; this is distinct from the ordinary hosted callback. Host construction/IPv4 formatting is in the acquire routine; no claim is made that a fixed port or all-interface bind is used.
3. Generates random state (`crypto/rand.Text`), PKCE verifier and SHA-256 challenge, starts HTTP callback handling, then opens the browser.
4. Callback validates state before accepting `code`; `callbackHandler.func1` @ `0x10236cb70` contains state query extraction, length/byte comparison, and error handling.
5. Calls `Config.Exchange` with `code_verifier`, shuts down the local server, and saves credentials (`loopbackLogin.LoginToken`, `NewBackendTokenStore`).
6. Resolves consumer/business tier and refreshes experiments via ACP manager (`fetchTier`, `newCodeAssistTierFetcher.func1` @ `0x10236cfe0`).

Thus the two login flows must not be merged into a single claimed redirect design. The presence of `127.0.0.1:4713` elsewhere is not evidence of the OAuth port.

### Post-login account operations

Reachable shared CodeAssistClient implementation has:

- `fetchLoadCodeAssistResponse` @ `0x101d0f4e0`: `loadCodeAssist`; alternative AICode `FetchLicenses`, with explicit missing-scope fallback to CCPA LoadCodeAssist.
- `OnboardUser` @ `0x101d10690`: `onboardUser`, project/tier onboarding.
- `FetchUserInfo` @ `0x101d11360` and quota/model/license retrieval. Business ACP `resolveEnterpriseLicense` rejects accounts without an appropriate Gemini Enterprise license and can require `gcp.project`/`gcp.location`.
- AICode/business methods include `fetchLicenses`, `fetchQuotaStatus`, `selfAssignLicense`, project lookup/search. Exact branch is account/flag dependent; not all are unconditional login requests.

### Other auth / provider routes

- **Gemini API key:** `GEMINI_API_KEY`, `geminiAPIKeyAuth.TryAuth` @ `0x101d7db50`, ACP `ExtractGeminiAPIKey`/`authenticateGeminiAPIKey`; ACP also supports an in-band supplied key, not only an environment value.
- **ADC / Vertex / Agent Platform:** `FindDefaultCredentialsWithParams`, `adcAuth.Authenticate` @ `0x101d79760`; `GOOGLE_CLOUD_QUOTA_PROJECT`, `GOOGLE_CLOUD_PROJECT`, `GOOGLE_CLOUD_LOCATION`; `AGY_ADC_AUTH` gates a CLI auth-chain option. ACP `authenticateAgentPlatform` @ `0x102368820` requires ADC plus project/location and explicitly rejects an unsupported Express Mode configuration.
- **Gateway:** configured gateway provider/API-key environment; ACP resolves user/admin gateway settings, can enforce MDM policy (`gateway.enabled` in `admin_settings.json`) and redacts auth metadata. Requests can go to a configured non-Google provider: do not assume every model request is sent only to Google.
- **Host auth:** flags `--host_bridge_url`, `--read_host_bridge_token_from_stdin`, `--use_host_auth`; HostBridgeService provides OAuth tokens from a host.
- **Enterprise/WIF/internal:** `AGY_CDE_ENTERPRISE_AUTH`, WIF flows, and internal LOAS/stubby options are compiled; presence is not proof these routes work for ordinary external accounts.

## 3. Token / local data storage

**Not Keychain-only.** `auth.NewCLITokenStorage` @ `0x101d7e090` constructs a composite of keyring and file storage. The normal product string is `antigravity` (internal alternative `jetski`); Keychain storage gets a CLI-specific service/name (`antigravity` in normal `NewServerBackend`; internal alternative `jetski-cli`). File path is constructed as:

- `<AppDataDir>/<product>-oauth-token`
- keyring-timeout marker: `<AppDataDir>/cache/<product>-keyring-unavailable`

`main.init.1` declares defaults `gemini_dir = .gemini`, `app_data_dir = antigravity-cli`. Consequently the normal root is **`~/.gemini/antigravity-cli`**, and the constructed normal token path is **`~/.gemini/antigravity-cli/antigravity-oauth-token`**; overrides and ACP backend selection can change the root/product. The path is derived from constructor/default code, not read from a user's disk. ACP `NewBackendTokenStore` @ `0x10236a940` uses the same product-suffix scheme; remembered ACP auth method is separately persisted by `RememberMethod` @ `0x10235fd80`.

Evidence for plaintext: `cliFileTokenStorage.updateStoredToken` @ `0x101d7e280` reads JSON, marshals JSON, calls `MkdirAll` with `0700`, then `os.WriteFile` with `0600`; `cliFileTokenStorage.LoadStoredToken` reads that JSON. Stored data includes OAuth token fields and account/project/region/tier metadata via `StoredToken`, `SaveProjectAndRegion`, `SaveUserTier`, `RememberIDToken`. Composite storage has explicit migration/healing logic (`migrateKeyringAndApplyFileMetadata`, `healSplitKeyringToken`, `saveTokenWithExistingFileState`), so a file may hold non-token metadata even when credentials are in Keychain; mere existence is not proof it contains a refresh token.

Fallback triggers in `NewCompositeTokenStorage` @ `0x101cdef80` include:

- caller requests file-based storage;
- `shouldBypassKeyring` @ `0x101cfa430` detects unsuitable environment;
- a recently recorded keyring timeout;
- keyring writes fail/are disabled (`disableKeyringWrites`, `memoizeKeyringTimeout`, `useFileIfKeyringWritesDisabled`).

The macOS backend is `github_com/zalando/go_keyring/...keyring.macOSXKeychain.Set/Get/Delete`. File permissions reduce cross-user exposure, but do not provide Keychain's access controls or protect refresh tokens from another process running as the same user, backups or inadvertently shared configuration. `os.WriteFile(...,0600)` also does not prove an already-existing file's mode is repaired. `Logout` methods remove stored credentials; no server-side token-revocation guarantee was established from this review.

Other local data: settings, keybindings, hooks/rules, projects, conversation history, logs, cache and updater status are represented in CLI store/backend code. `NewServerBackend` initializes `settings.json`, `keybindings.json`, `hooks.json`, `rules.json`; logging can include workspace paths and errors. No user files were opened to enumerate actual retained contents.

## 4. Endpoint inventory

| Host / endpoint | Purpose / data | Evidence and confidence |
|---|---|---|
| `accounts.google.com/o/oauth2/auth` | Browser OAuth; client ID, scopes, redirect, PKCE challenge, state | Exact global constant at `0x103cb0511` VA |
| `oauth2.googleapis.com/token` | Code exchange and refresh; code/verifier, refresh token, client credentials | Exact global `0x103c8435d` VA, OAuth methods |
| `www.googleapis.com/oauth2/v2/userinfo` | Account profile/email/identity | `fetchUserInfo`, `fetchAdcUserInfo` |
| `antigravity.google/oauth-callback` | Ordinary CLI OAuth redirect through hosted page | `StartInteractive` @ `0x101d7adfc` |
| `localhost:<ephemeral>/auth/callback` | ACP browser OAuth callback | `net.Listen("localhost:0")`, ACP handler |
| `cloudcode-pa.googleapis.com` | Production CCPA inference/account RPCs, `loadCodeAssist`, `onboardUser`, `generateContent`, `streamGenerateContent`, user settings | `CLIAuthProvider.GetEndpointURL`, CodeAssistClient; exact host constant |
| `daily-cloudcode-pa.googleapis.com` | Alternative/defaulted CLI endpoint in reviewed startup paths | `main.init.func3` @ `0x10331fb7c`, startup/config code; not safe to assume every CLI account uses production host |
| `aicode.googleapis.com` | AICode licenses/quota/model inference routes | String file offset `0x3c49f05`, `StreamGenerateContentFromAICode`, `FetchLicensesFromAICode` |
| `play.googleapis.com/log` | Clearcut structured telemetry batches | Exact string file offset `0x3c5ea0a`; `clearcutLogger.Log` @ `0x102973f70` calls Clearcut client |
| CCPA endpoint, `sendTelemetry` / analytics-related RPCs | Shared alternate telemetry transport / account-dependent routing | Embedded `sendTelemetry`, telemetry wrapper/BAIC clients; do not treat all telemetry as one unconditional destination |
| `generativelanguage.googleapis.com` / configured Gemini API endpoint | Gemini API-key inference | Gemini API-key provider and shared model client; exact routing is provider/config dependent |
| Vertex `aiplatform.googleapis.com` / regional endpoint | ADC-backed project/location model requests | `StreamGenerateContentFromVertexAI` @ `0x101d13620`; dynamic project/location URL construction |
| Configured gateway / HTTP MCP endpoints | Prompts, tool arguments/results, provider credentials or configured headers | `GenerateContentFromGateway`, MCP HTTP/stdio registration; destination is user/admin supplied |
| `antigravity-cli-auto-updater-974169037036.us-central1.run.app/manifests/darwin_arm64.json` | Version manifest, payload URL, SHA-512 | Script lines 11, 98–137; `GetManifestURLFromBase` @ `0x1032c7480` |
| Manifest-selected payload host (GCS in supplied distribution context) | CLI release download | Neither script nor reviewed update path provides a publisher signature independent of manifest |

Internal/staging constants such as `gaiastaging.corp.google.com`, `preprod-aicode...`, `autopush-cloudcode-pa.sandbox.googleapis.com`, and preprod/daily variants also exist. They are not evidence of ordinary-user traffic. No active Sentry/Statsig upload path was established for this CLI.

## 5. Telemetry and privacy inventory

### Transport and identity

CLI analytics is a real worker, not a logging-only stub: `analytics.Init` @ `0x101d596c0`, `recordEventSafely` @ `0x101d595b0`, and `telemetry.Client.RecordAnalyticsEventString` @ `0x102bcf700` lead to `AntigravityClient.RecordAnalyticsEventString` @ `0x102976b50`, protobuf construction, and Clearcut logging. The wrapper explicitly adds `ideVersion`, `ideName`, `os`, **`machineId`**, `subclientType`, `environmentType` to analytics metadata.

The protobuf `devtools/jetski/telemetry/telemetry.proto` at file offset **`0x3f2a55c`** defines `JetskiTelemetryExtension` with `ide_name`, `ide_version`, `outer_app_version`, `os`, `arch`, `user_tier_id`, `user_tags`, `region_code`, `product_name`, `host_identifier`, `enabled_labs_features`, `experiment_config`, `hashed_gaia_id`, `is_redacted`, and the content payload variants listed below. `setMetadataFields` @ `0x102974fc0` stamps telemetry metadata. A hashed Google identity remains linkable/pseudonymous, not anonymous.

**Qualification:** `machineId` is a populated map key, not proof of a hardware fingerprint. Some standalone initialization passes zero/empty identity arguments (`main.init.func3` @ `0x10331fba0`); a nonempty durable hardware ID, hostname, serial number or network-interface MAC being sent on every CLI run was not established. Do not replace this nuance with a claim that all events contain a device fingerprint or raw email. Email/profile identity is processed for auth; raw email in every analytics event was not established.

### Data-inventory table

“Policy-dependent” means the client has redaction/collection controls, not that consent or collection was observed in a running session.

| Data element | Source | Destination host / endpoint | Trigger | Opt-out / limits? |
|---|---|---|---|---|
| Google email/profile, subject / ID-token claims | OAuth userinfo / OIDC | Google OAuth/userinfo; local token storage; account RPCs | Login, validation/refresh | Avoid Google login by using configured alternative auth where supported; needed for selected account flow |
| Refresh/access/ID tokens, project/region/tier | OAuth/ADC/auth provider | Google token endpoint; Keychain or local JSON | Login and refresh | Logout removes local credential through storage API; not established to revoke server-side grant |
| CLI version, OS/arch, IDE/product/surface, environment type | Runtime metadata | Clearcut; account-specific shared telemetry paths | Analytics events | Disable/redaction controls below; standalone operational analytics separate from UI setting |
| Machine-ID metadata slot; host identifier, hashed Gaia ID, account tier/tags | Metadata provider / account | Clearcut structured telemetry | Metadata stamping when values available | Policy-dependent; empty standalone identity possible; no proof of hardware-derived fingerprint |
| Startup phase timings, time to first token, print-mode options, model/agent selection booleans | CLI startup and print runner | Clearcut | Startup, `--print`, stream-input session | Controls below; timing/config counters are not prompt text |
| Tool name, confirmation surfaced/responded/dismissed, decision/scope, permission mode | Approval UI / tool scheduler | Clearcut | Tool confirmation and execution | Controls below; separate from execution approval |
| ACP client identity/version, capabilities/authentication method/session markers | ACP connection and auth | Clearcut | ACP connection/session/login | `--disable_telemetry` / `--disable-telemetry`; capabilities sanitized by `sanitizeACPValue` |
| Terminal/environment feature classification; settings/config override indicators | Environment and CLI settings | Clearcut | Startup/settings snapshot | Records many booleans and categories; **not proof of complete environment-variable values or secret dumps** |
| Plugin action/name/source type, import/enable/disable/install outcome | Plugin manager | Clearcut | Plugin command/action | Action recorder analytics; code includes bounded/sanitized categorization, not proof arbitrary plugin file contents are always sent |
| Voice usage, duration/error/session statistics | Dictation/live voice | Clearcut analytics | Voice/dictation actions | Distinct from audio sent to model/transcription service; no claim telemetry includes raw audio in these counter functions |
| Prompt/messages, system instruction, model/tool schemas, source-file excerpts, images/media and tool results used by model | Conversation and selected workspace/tool context | CCPA/AICode/Gemini/Vertex/configured gateway | Agent turn/generation | Required for remote inference; telemetry opt-out does **not** make inference local |
| Command line, working directory, stdout/stderr, file URI/content, MCP arguments/results in trajectory steps | Cortex tool execution / trajectory | Clearcut trajectory payload path; model/provider as context | Trajectory/step recording and subsequent inference | Collection/redaction policy-dependent; full schema supports this content, actual field population varies |
| Workspace URIs, artifacts, state ID, sensitivity level | Agent state | Telemetry `state_initialization_data` payload | State initialization recording | Policy-dependent, redaction implemented |
| Error messages/status/stack traces | Error-handling paths | Clearcut errors/observability; local logs | Failures/panics | Errors can contain paths/content [INFERENCE]; redaction annotations/handling matter |
| Version/platform/network IP metadata inherent in release request | Updater HTTP | Cloud Run manifest and selected payload server | Automatic update check, manual update/install | `AGY_CLI_DISABLE_AUTO_UPDATE=true` suppresses automatic worker trigger; unrelated to telemetry setting |
| Remote instance name/session/daemon events | Remote-control CLI and user-provided name | Remote-control infrastructure plus analytics | Opt-in remote-control/daemon command | Feature opt-in; daemon may persist until stopped/unregistered |

### Events observed in CLI analytics functions

Examples directly represented by functions/strings, rather than generic telemetry guesses:

- `RecordACPClientConnected`, `RecordACPSessionStarted`, `RecordACPAuthenticated`.
- Startup metrics (`MarkStartupPhase`, `ReportStartupTime`, `ReportTimeToFirstToken`).
- `RecordPrintModeInvoked`, `RecordStreamInputSession`, `RecordEditorModeConfig`, `RecordAutoModeEntered`.
- `tool_confirmation_surfaced`, `tool_confirmation_responded`, `tool_confirmation_dismissed`, `conversation_picker_used`.
- `RecordKeyringError`, `RecordSignInError`, `RecordSettingsLoadError`, `RecordDaemonCommand`.
- `RecordEnvOverrides`, `RecordModelConfigOverrides`, `RecordPluginAction`, `RecordSettingsSnapshot`, `RecordTerminalEnv`.
- `RecordDictation`, `RecordDictationNoAudio`, `RecordDictationFailed`, `RecordVoiceCommand`, `RecordLiveVoiceStartFailed`, `RecordLiveVoiceSession`, `RecordMarkdownFailure`, `RecordShortcut`.

Evidence: `final-evidence.txt`, especially analytics functions at `0x101d582b0`–`0x101d623e0`. Print-mode analytics includes configuration indicators such as `agent_set`; do not infer raw `--print` prompt capture from that event alone.

### Exact content-capable schemas

Descriptor extraction makes the privacy exposure concrete:

- `third_party/jetski/analytics_pb/analytics.proto`, offset **`0x3f43732`**:
  - `RecordCommandUsageRequest`: `metadata`, `command`, `selection`, `request_source`, `prompt_id`, `completion_id`, `completion`, **`prompt`**, `requested_model_id`, `stop_reason`, `language`, `provider_source`, `command_prompt_components`, `cortex_trajectory_reference`, latency/profile data.
  - `RecordPromptRequest`: `request`, `prompt_components`, `unified_prompt_components`, timestamps and server/provider metadata.
  - `RecordCompletionsRequest`: completions, relative path, repository, prompt/model metadata.
  - `RecordCortexTrajectoryStepRequest`: full `step` plus trajectory reference.
- `devtools/jetski/telemetry/extensions/cortex_trajectory.proto`, offset **`0x3def836`**: trajectory/cascade IDs, parents, trajectory metadata/source/type and redaction information.
- Shared Cortex descriptor:
  - `CortexStepViewFile`: `absolute_path_uri`, **`content`**, **`raw_content`**, media/binary data, offsets/ranges.
  - `CortexStepRunCommand`: **`command_line`**, `proposed_command_line`, `cwd`, exit code, sandbox decisions, **`combined_output`**, raw debug output, **`stdout`**, **`stderr`**, buffers/snapshots.
  - `CortexStepUserInput`: input items/query/images/media, artifact/file comments, client config and identity metadata.
  - `CortexStepMcpTool`: server name, **tool_call**, **result_string**, result URI, images/media, server info.
  - `StateInitializationData`: timestamp, state ID, **workspaces**, **artifacts**, sensitivity level.

These are **capable payload definitions**, with direct recording methods (`RecordCortexTrajectory`, `RecordCortexTrajectoryStep`, `RecordStateInitializationData`), not a finding that every command or file is always uploaded. Redaction paths exist and must be included in any fair assessment.

### Opt-out / redaction boundaries

- ACP registers **`--disable_telemetry` and `--disable-telemetry`**, described “If true, disable telemetry”, at `0x10301f2b4` / `0x10301f2e4`. These are explicitly ACP flags; do not assume every ordinary root/subcommand accepts them identically.
- CLI store implements `SetTelemetryEnabled` @ `0x102f5d4f0`, `syncTelemetryFromServer` @ `0x102f5cd80` and `propagateTelemetry` @ `0x102f5d6d0`; the shared user setting is `telemetry_enabled` / protobuf JSON `telemetryEnabled`, with `user_data_collection_force_disabled` also represented in server settings.
- Server-setting load failure logs **“Failed to read telemetry setting from server, defaulting to disabled”**. Propagation is skipped if not logged in or using business auth. There is a real UI/server preference, not merely an undocumented environment toggle.
- The Clearcut client includes `EnableDeferredRedaction`, `payloadIsOptedOut`, `redactPayload`, `stripInlineMedia`, `stripSnapshotIdentityRecursive`, and annotation-driven `telemetryutil.Redact`. Timeout logs **“Telemetry redaction decision took over %v; redacting until it returns”** (`0x10297aa64`): conservative redaction while resolving policy, not evidence of fail-open content upload.
- **Redaction is not necessarily no traffic.** The pipeline can strip payload/user data while still logging operational/analytics events. `NewStandaloneRecordAnalyticsEventFn` @ `0x102bce5e0` is used before/without a full server backend, including daemon/subcommand startup; its callbacks are a no-op and a constant false-returning closure (`0x102bd0600`, `0x102bd0610`). The CLI's ordinary `recordEventSafely` does not itself consult the UI store setting. Thus there are distinct operational-analytics and content-collection decisions; a claim that toggling the UI preference guarantees zero network telemetry is unsupported.
- No independently verified `DO_NOT_TRACK` or universal `AGY_*TELEMETRY*` environment opt-out was recovered. `AGY_CLI_DISABLE_AUTO_UPDATE=true` controls updates, **not telemetry**.

## 6. Agent loop, tools, approvals and sandbox

### Model path / request shape

`cli/backend/backend.NewServerBackend` @ `0x10230a240` wires CodeAssistClient, auth storage, metadata, projects/customizations and telemetry. The binary contains the Cortex executor, tool scheduler, model orchestration, summarization, local file and terminal handlers, MCP, browser support, remote agents and swarm/subagents.

Two exact request envelopes from extracted descriptors:

- `google/internal/cloud/code/v1internal/prediction_service.proto` at **`0x3f75e0e`**: `GenerateContentRequest { project, request_id, request, model, user_prompt_id, user_agent, request_type, enabled_credit_types }`; response has `response`, trace ID, consumed/remaining credits and safety/remote-context metadata.
- `google/gca/aicode/v1main/prediction_service.proto` at **`0x41bcbb2`**: `GenerateContentRequest { model, aicode, contents, system_instruction, tools, tool_config, labels, safety_settings, generation_config, session_id, entitlement }`.
- `google/gca/aicode/v1main/aicode_content.proto` at **`0x3e14da3`**: `SourceFile { inclusion_reason, file_uri, programming_language, segments }`, `FileSegment { content, is_selected }`, `CodeBlock { content, programming_language }`, edits/diffs.

The implemented flow is model request → streamed response/tool calls → scheduled tool execution with permission/sandbox decisions → results back into context → further turns, with state/trajectory recording. Provider implementations include CCPA, AICode, BusinessAICode, VertexAI and gateway (`CodeAssistClient.StreamGenerateContent*` / `GenerateContent*`). Model choice and tool exposure depend on the agent/model configuration and experiment flags; a compiled tool is not automatically available to all models.

### Compiled tool surface

Direct Cortex step schemas and handler/tool symbols show these families:

- Files/code: view/read file, list directory, find, grep search, code-item lookup, write/replace file, file changes, delete/move, git commit, diagnostics/compile, notebook read/edit/execute.
- Terminal: run command, command status, send command input, persistent/asynchronous commands, stdout/stderr and exit status.
- Web/browser: web search, read URL/content chunks, open URL, browser subagent, read page/DOM, execute browser JavaScript, screenshots/pixel clicks, console logs.
- MCP: list/read resources, dynamic MCP tools, `CallMcpToolTool`, MCP app views; arguments/results can enter model context.
- Coordination: ask question/custom permission, update steps/focus, run workflow, tasks, agent/subagent/swarm delegation, memory/knowledge-base retrieval.
- Additional deployment/cloud/app tooling is compiled (Firebase, Cloud SQL, web-app deploy/status), but activation for this CLI session is not established.

Evidence: `third_party__jetski__cortex_pb__cortex.proto.json`, `functions.txt`, including `cortex/core/tools/tools.(*CallMcpToolTool).Name` @ `0x102229300`, list/read resources tools, `cortex/core/native_swarm/...`, CLI `swarm/*` and browser interfaces. Tool schemas are stronger evidence than a bare word such as “docker” in a string pool.

### Modes and controls

- `--dangerously-skip-permissions` explicitly **auto-approves all tool permission requests**; `--sandbox` explicitly enables terminal restrictions. They are separate options in both root and ACP registration.
- Agent modes accepted by `AgentMode.Valid` @ `0x100e86f70`: empty/default, **`auto`**, **`plan`**, **`accept-edits`**, **`bestn`**. Visibility/availability can be feature-gated.
- Permission modes accepted by `PermissionMode.Valid` @ `0x100e86dd0`: **`strict`**, **`always-proceed`**, **`request-review`**, **`proceed-in-sandbox`**.
- `ServerBackend.populatePermissionConfig` @ `0x1023132a0` calls `FilterUnsandboxedRules`, `workspacePaths`, `PermissionGrantsFor`, and handles terminal sandbox/non-workspace policy. The diagnostic explicitly names `toolPermission`, `autoExecPolicy`, `enableTerminalSandbox`, `allowNonWorkspaceAccess`. String rules include `command(*)`, `unsandboxed(*)`, `execute_url(*)`, `write_file(...)`.
- macOS sandbox implementation is real Seatbelt/exebox code: `cortex/command/command.prepareSandbox` @ `0x1015e7310`; `devtools/ai/sandbox/exebox.wrapSandboxExec` @ `0x100afaa40` writes a profile and invokes **`/usr/bin/sandbox-exec`**; proxy/network audit rules and environment handling are compiled. This is **not evidence of Docker isolation** or that the entire CLI process is sandboxed. The flag specifically describes terminal restrictions.
- **No proven sandbox escape, permission bypass or guaranteed all-tools sandbox was established.** Safest deployment assumption is that unrestricted CLI process/MCP/plugin code has the user's authority unless a concrete restrictive path is selected and verified.

### Headless / MCP / extensions / subagents

- `--print` / `-p` / `--prompt`: single noninteractive prompt. Text, JSON and stream-JSON output; NDJSON stream input; optional JSON schema; timeout; conversation resume/continue; model, effort, agent, project and mode selection.
- Print-mode implementation has `headlessDenialNotice` (`0x1032b60e0`), rather than proof that interactive approvals silently succeed. Explicit auto-approval remains a major unattended-use choice.
- `acp`: stdio JSON-RPC server with optional process-per-session worker, host IDE MCP port, isolated-session worker flag, separate auth manager.
- `mcp add`: stdio or HTTP; arbitrary command/arguments for stdio, repeated environment variables, or HTTP headers. These are a deliberate local execution/data-transfer trust boundary. Normal CLI registration rejects HTTP headers for stdio and command arguments for HTTP.
- `plugin` / `plugins`: local directory/marketplace/URL installation, zip validation, import from Gemini/Claude, enable/disable, marketplace linking, MCP setup and hook/context-provider support. Messages explicitly include **“disabled pending permission review — run /plugin enable ... after reviewing”** for a reviewed installation path. Do not describe all plugin installs as immediate unreviewed execution.
- Hooks, custom agents, skills, rules, context providers, plugin sidecars and remote agents extend capability. CLI `swarm/*` plus native-swarm handlers implement multiple workers/subagents; using a remote agent moves the execution/data boundary to a remote machine.

## 7. Updater / `agy install` / bootstrapper

### Native updater

- Manifest base: `https://antigravity-cli-auto-updater-974169037036.us-central1.run.app`.
- URL builder `GetManifestURLFromBase` @ `0x1032c7480`: `%s/manifests/%s_%s%s.json` (platform/musl suffix supported); macOS arm64 resolves `darwin_arm64.json`.
- `triggerUpdateShared` @ `0x1032c7650` uses a lock and a 15-minute freshness check, and spawns the current executable as background worker with `--bg-updater` and relevant directory settings. Background worker/relaunch paths are explicit, not merely installer marketing.
- Automatic trigger opt-out is the exact environment string comparison **`AGY_CLI_DISABLE_AUTO_UPDATE == "true"`** (`0x1032c7698`–`0x1032c76c8`). Do not document `1` as equivalent based on this code. Manual force/update has separate control flow.
- `runUpdateShared` @ `0x1032c8960`: retrieve manifest, compare version, GET manifest-selected URL, stream payload through SHA-512, compare digest, optionally extract named tar member, replace binary using rename/retry/rollback, write `update_status.json`.
- Integrity mismatch string: **“integrity failure: checksum mismatch”** at `0x1032ca7fc`/`0x1032ca834`. Reviewed path contains SHA-512 verification, but no independent manifest/payload digital-signature or macOS designated-requirement verification before swap. SHA-512 proves agreement with that manifest, not publisher authorization if the manifest is compromised.
- Parsing the payload URL around `0x1032c9734` is used to detect `.tar.gz` from URL path; **it is not a host allowlist or HTTPS-enforcement check**. The supplied HTTPS base is good, but a `--release_base_url` override and manifest URL trust remain deployment concerns. This does not show a network attacker can modify HTTPS responses.
- `--release_base_url` permits an alternate release server; `--bg-updater` is exposed. No interactive consent checkpoint was observed in the reviewed automatic update worker path. The installer explicitly says background self-updating occurs during regular runs (lines 57–63).

### Native shell-profile setup

`installer.configureProfiles` @ `0x1032e9da0` enumerates:

- `~/.bashrc`
- `~/.zshrc`
- `~/.zprofile`
- `~/.bash_profile`
- `~/.profile`
- `~/.config/fish/config.fish`

It inspects existence/readability, follows symlinks with `EvalSymlinks` where appropriate, reads profiles, purges conflicting aliases unless `--skip-aliases`, and appends/prepends PATH configuration unless `--skip-path`. It uses a temporary profile, restores permissions, then renames the replacement; this is better than blindly overwriting without preserving mode, though it still edits multiple shell startup files. Missing/unreadable profile paths are handled/skipped in the routine rather than proof every path is always created.

Exact generated PATH statements:

```sh
export PATH="%s:$PATH"
```

```fish
set -gx PATH "%s" $PATH
```

Evidence: `0x1032ea3e8` / `0x1032ea3a4`; defaults use the installed binary directory, normally `$HOME/.local/bin`, custom `--dir` supported. These format strings do not shell-escape `$`, backticks, quotes or newlines in the inserted path: e.g. a literal directory component containing `$()` becomes shell code when the profile is sourced. This is a path-to-code injection issue conditional on a crafted installation path, not an unauthenticated remote exploit. Recommended remediation: proper shell-specific literal quoting and a clearly delimited managed block; never interpolate an untrusted path into double-quoted shell syntax.

### Bootstrapper findings

Exact line evidence in `install.sh`:

- Lines 11–12: release base and `$HOME/.local/bin` default; custom `-d|--dir` accepted.
- Lines 98–137: fetch manifest via `curl -fsSL` or `wget -q -O -`; JSON extraction is a greedy `sed` expression, not a JSON parser. No signature/authenticated metadata chain or rollback metadata is enforced.
- Lines 147–170: predictable `$HOME/.cache/antigravity/staging` and fixed `agy`/`agy.tar.gz` file names with exit cleanup; not `mktemp`. [INFERENCE] Concurrent installs can interfere; a writable/symlink-contaminated staging directory can redirect/clobber files. No cross-user exploit is claimed when the home directory is correctly protected.
- Lines 172–198: manifest-controlled URL is downloaded and SHA-512 checked against the same manifest; no explicit allowed payload host/scheme list, no independent code-signature verification. Curl/wget redirects are followed according to their normal options.
- Lines 202–224: directory creation, named tar member extraction when tarball, copy to target, set executable permission. No root/sudo requirement.
- **Lines 226–229: `xattr -d com.apple.quarantine "$BINARY_PATH" ... || true`** intentionally suppresses quarantine on macOS.
- **Lines 231–239: executes `"$BINARY_PATH" install [--dir ...] || true`**, so the fetched code immediately gains the installing user's privileges and can edit startup profiles; native install failure is ignored.

The supplied installer is not intrinsically evidence of malware. However, a curl-to-shell bootstrap plus same-origin checksum, quarantine removal, automatic execution and background replacement creates a strong supply-chain trust dependency on the release service. Prefer a verifiable signed release/manifest with a pinned expected publisher; keep Gatekeeper's normal checks; make update policy and shell modifications explicit.

## 8. Subcommands and flags (static inventory)

This is recovered registration/help evidence, **not output from running `agy --help`**. The CLI uses Google's Go flag library, not evidence of Cobra. Some flags are internal, feature-gated or scoped to a subcommand. Source-addressed descriptions of every decoded named registration are in **`ALL_FLAGS.txt`** / machine-readable **`flags.json`**; blank retired-flag placeholders are excluded from the list below.

### Registered executable subcommands

- No subcommand: interactive TUI (or `--print` etc).
- `acp` — ACP stdio server.
- `remote-control start|status|stop` — daemon registration/start, status, unregister/stop.
- `mcp add|remove|list|enable|disable` — MCP configuration.
- `models` — list available models.
- `update` — update CLI.
- `plugin` and alias `plugins`: `list [--json]`, `import [source]`, `install <target>`, `uninstall <name>`, `enable <name>`, `disable <name>`, `validate [target] [--strict] [--json]`, `link <marketplace> <target>`, `help` (plus `validate --help`).
- `changelog` — release notes.
- `install` — native PATH/alias configuration.
- `agent` and alias `agents` — available agents.
- `mic-serve` — serve microphone to a CLI on another host.
- `help [subcommand]`.

Evidence: `main.init.0`, `.init.2`, `.init.3`, `.init.4`, `.init.5` registration maps; `usage-headers.txt`, `command-help.txt`, plugin `printUsage` @ `0x102ce96c0`. Ordinary `login`/`logout` exist as interactive auth actions, **not established as registered top-level executable subcommands**; do not invent `agy login` from the existence of `RunLogin`.

### Root/session flags

```text
--print, -p, --prompt
--print-timeout
--output-format       text | json | stream-json
--input-format        text | stream-json
--json-schema
--model
--effort              low | medium | high | xhigh | max
--agent
--conversation
--continue, -c
--prompt-interactive, -i
--dangerously-skip-permissions
--disable-slash-commands
--mode
--sandbox
--add-dir             repeatable
--settings            inline JSON or file; session-only settings
--project
--new-project
--remote-agent
--remote-control
--remote-control-name
--hub
--hub-port
--app_data_dir
--gemini_dir
--bg-updater
--use_local_chrome
--release_base_url
--log-file
--csrf_token
--host_bridge_url
--read_host_bridge_token_from_stdin
--use_host_auth
```

### ACP-scoped registered flags

```text
--stdio
--process-per-session
--worker
--agent
--ide-mcp-port, --ide_mcp_port
--add-dir
--sandbox
--dangerously-skip-permissions
--login
--include-default-customizations
--log-file, --cli_log_file
--gemini_dir, --gemini-dir
--app_data_dir, --app-data-dir
--cli_dir_name
--cloud_code_endpoint
--disable_telemetry, --disable-telemetry
--is_google3_workspace
--is_google_internal   deprecated
--use_stubby_auth
--override_user_agent_name
```

### Other subcommand flags

```text
remote-control start: --session, --name
mcp add:              --type/-t (stdio|http), --env/-e (repeatable),
                      --header/-H (repeatable)
mic-serve:            --addr
install:              --skip-aliases, --skip-path, --dir
plugin:               --json, --strict, --help (usage text above)
subcommand parsing:   --help/-h
bootstrap install.sh: -d/--dir, -h/--help
```

### Linked common/internal flag registrations

These are decoded registrations, not a recommendation to use internal Google flags:

```text
--undefok
--syslog_on_start
--util_status_save_stack_trace
--log_dir
--log_link
--logbuflevel
--version
--disable_log_to_disk
--v
--vmodule
--log_backtrace_at
--logtostderr
--alsologtostderr
--stderrthreshold
--silent_init
--mlock_style
--AuditLogPath
--AuditLogVerbose
--protosanitizer_use_old_annotation_sets
--protosanitizer_use_streamz_for_sanitization_monitoring
--protosanitizer_use_sensitivity_based_logic
```

`syslog_on_start` help explicitly says logs are collected to identify used vs obsolete binaries; registration alone does not establish that it is enabled on macOS. `--disable_log_to_disk` concerns local logs, not all telemetry.

### Interactive slash-command surface

Compiled command implementations include add-workspace-directory, agents, artifact, btw, changelog, clear, context, copy, credits, diff, effort, exit, feedback, fork, help, hooks, keybindings, resume, logout, mcp, model, open, permissions, plugin, projects, remote-control, rename, rewind, search, config/settings, skills, statusline, tasks, title, usage, dictate, plus generic/dynamic skills and feature-gated commands. Evidence: `cli/commands/commands.(*...Command).Name` symbols in `functions.txt`. These are not all independent shell subcommands; exact user-visible aliases are generated by their `Name`/registry implementations and can vary with feature flags.

## 9. Assessment / practical controls

1. Treat the CLI as a remote AI agent with filesystem, shell, browser, MCP and plugin authority, not a local-only text editor. Use a dedicated low-privilege environment and tightly scoped workspaces for sensitive repositories.
2. Set a deliberate approval policy and select `--sandbox`; never assume `--dangerously-skip-permissions` implies confinement. Independently constrain MCP/plugin executables.
3. For confidential material, decide inference routing and telemetry/content collection separately. The model still receives necessary prompt/context when optional telemetry is disabled. Do not promise zero egress from one UI toggle.
4. Keep credentials in Keychain where available; monitor for fallback files/markers under the configured app-data root, exclude them from backups/sharing where appropriate, and maintain restrictive permissions. Do not inspect a user's tokens without separate authorization.
5. For managed deployment, pin a reviewed binary/hash through an independent trusted channel, set `AGY_CLI_DISABLE_AUTO_UPDATE=true`, and use a controlled signed-release pipeline rather than unconsented replacement. Same-source manifest hashes alone are not a publisher-authentication mechanism.
6. Avoid crafted/metacharacter-containing install directories until shell quoting is fixed, and review shell-profile changes before sourcing them.

### Limits that must survive consolidation

- No binaries executed, network requests made, login performed, user secret/config directories read, or live provider behavior observed.
- No claim of universal raw-email/hardware fingerprint collection; no claim that every content-capable schema is always sent.
- No claim that all telemetry is absent after opt-out; redaction and optional content collection differ from operational counters.
- No claim embedded native-client secrets are a critical leak.
- No claim a generic device-flow URL proves a supported CLI device flow, a Docker string proves containerization, or shared LS packages imply identical runtime settings.
- No proof of a sandbox escape, zero-click remote exploit, server retention period, training use, or active exfiltration of a particular user's files.
