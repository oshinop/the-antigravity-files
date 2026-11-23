# Sign-in and plans: how `agy` 1.5.14 authenticates and what the account unlocks

`agy` signs in with Google OAuth through a page that Google hosts, and the user pastes the resulting code back into the terminal. It stores the token in the OS keyring, or in a plain JSON file when no keyring is available. A Gemini API key is the other way in. With a key, `agy` skips the Cloud Code backend entirely and falls back to switch values built into the binary. With a Google account, the server decides the model list, the quotas `/usage` shows, and which multi-agent commands appear. On one paid plan, those commands even differ by model.

## The sign-in chain tries stored credentials first, then offers Google OAuth or a Cloud project

Without a stored token, the start screen reads "You are currently not signed in." and offers "1. Google OAuth" and "2. Use a Google Cloud project". `cli/backend/auth.(*ChainedAuth).Authenticate` walks a list of methods. The ones compiled in are the OS keyring (`keyringAuth`), consumer OAuth (`oauthMethod`), Application Default Credentials (`adcAuth`), a Gemini API key (`geminiAPIKeyAuth`), a gateway (`gatewayAuth`), workload identity federation (`wifOAuthMethod`) and an internal corporate login (`externalCorpLoginAuth`). The second menu entry goes through `(*ChainedAuth).StartGCPAuthProcess` and uses a separate OAuth client, `884354919052-36trc1jjb3tguiac32ov6cod268c5blh.apps.googleusercontent.com`.

Evidence:
- Binary: `jetski/cli/backend/auth.(*ChainedAuth).Authenticate`, `jetski/cli/backend/auth.(*ChainedAuth).StartGCPAuthProcess`, `jetski/cli/backend/auth.(*adcAuth).Authenticate`, `jetski/cli/backend/auth.(*geminiAPIKeyAuth).TryAuth`, `jetski/cli/backend/auth.getOauthParams` (both client IDs)

## Google OAuth uses PKCE, asks for seven scopes and redirects to a page Google hosts

`cli/backend/auth.(*oauthMethod).StartInteractive` builds the request. It sets the redirect URI to `https://antigravity.google/oauth-callback`, creates a PKCE verifier (`oauth2.GenerateVerifier`) and an S256 challenge (`oauth2.S256ChallengeFromVerifier`), and creates the `state` value from 16 random bytes in base64 (`auth.generateState`). It reads `SSH_CLIENT`, `SSH_TTY` and `SSH_CONNECTION`, then opens the browser in a goroutine (`auth.OpenBrowser`) and also prints the link. The link it prints is:

```text
https://accounts.google.com/o/oauth2/auth?access_type=offline
  &client_id=1071006060591-tmhssin2h21lcre235vtolojh4g403ep.apps.googleusercontent.com
  &code_challenge=[challenge]&code_challenge_method=S256
  &prompt=consent
  &redirect_uri=https%3A%2F%2Fantigravity.google%2Foauth-callback
  &response_type=code
  &scope=https://www.googleapis.com/auth/cloud-platform
         https://www.googleapis.com/auth/userinfo.email
         https://www.googleapis.com/auth/userinfo.profile
         https://www.googleapis.com/auth/cclog
         https://www.googleapis.com/auth/experimentsandconfigs
         https://www.googleapis.com/auth/aicode
         openid
  &state=[state]
```

The first five scopes are the binary's base scope list, which the Cloud project login uses as it is. `auth.getScopesByAuthMethod` adds `openid` and, conditionally, `aicode` for consumer sign-in. `cloud-platform` is Google Cloud's broad access scope. `cclog` covers Clearcut logging and `experimentsandconfigs` covers the server switches. `access_type=offline` with `prompt=consent` asks for a refresh token every time.

When the scope is missing from a token, the binary logs "OAuth token lacks %s; routing AICode traffic to CCPA for", and the backend has a warning for that case (`(*ServerBackend).canWarnMissingAICodeScope`).

Evidence:
- Terminal: the sign-in screen printed the link above (1.5.14)
- Binary: `jetski/cli/backend/auth.(*oauthMethod).StartInteractive` calls `golang/oauth2.GenerateVerifier`, `jetski/cli/backend/auth.generateState`, `golang/oauth2.S256ChallengeFromVerifier`, `golang/oauth2.(*Config).AuthCodeURL` and, in a goroutine, `jetski/cli/backend/auth.OpenBrowser`
- Binary: `jetski/cli/backend/auth.getScopesByAuthMethod`; strings `https://antigravity.google/oauth-callback`, `aicode_scope_missing`

## The user carries the code from Google's page back to the terminal

Google redirects the browser to `https://antigravity.google/oauth-callback?state=…&iss=https://accounts.google.com&code=…&scope=…&authuser=0&prompt=consent`. The page is titled "Google Antigravity Authentication" and says "Paste this code into your application to complete authentication:", with a "Copy to Clipboard" button. The same page also shows a "How did you hear about us?" survey. Nothing listens locally: the terminal waits at "If you aren't automatically redirected, paste the authorization code below:".

`(*oauthMethod).SubmitAuthorizationCode` accepts either the bare code or a pasted URL. If the text starts with `http://` or `https://`, it parses it and takes the `code` query parameter. It then calls `completeOAuth`, which exchanges the code with `oauth2.(*Config).Exchange`, reads the email from the ID token (`codeassistclient.ExtractOIDCClaims`), fetches the profile (`auth.fetchUserInfo`) and records sign-in errors to telemetry (`analytics.RecordSignInError`). Neither function compares a returned `state` with the one it generated. The PKCE verifier, which never leaves the process, is what ties the code to this sign-in.

The exchange is a form `POST` to `oauth2.googleapis.com/token` carrying `code`, `code_verifier`, `redirect_uri`, `client_id`, `client_secret` and `grant_type`. The client secret is built into the binary, as it is for any installed OAuth client. The response holds `access_token`, `refresh_token`, `id_token`, `expires_in`, `scope` and `token_type`. Refreshes use the same endpoint with `refresh_token` (`auth.refreshAndSaveStoredToken`). A refresh token that is no longer valid comes back as `invalid_grant`.

Evidence:
- Traffic: [token exchange, refresh and userinfo](../../resources/cli/protocol/wire-account-and-config.md#sign-in)
- Binary: `jetski/cli/backend/auth.(*oauthMethod).SubmitAuthorizationCode` (prefix check, `net/url.Parse`, `code` lookup) calls `jetski/cli/backend/auth.(*oauthMethod).completeOAuth`; its closure calls `golang/oauth2.(*Config).Exchange`, `jetski/language_server/code_assist_client/codeassistclient.ExtractOIDCClaims`, `jetski/cli/backend/auth.fetchUserInfo` and `jetski/cli/analytics.RecordSignInError`
- Binary: `jetski/cli/backend/auth.refreshAndSaveStoredToken`

## The token goes to the OS keyring or a plain JSON file

`cli/backend/auth.NewCLITokenStorage` sets up a keyring store with a file store behind it (`codeassistclient.NewCompositeTokenStorage`). The keyring goes through the `go-keyring` library (`golang/github_com/zalando/go_keyring/v/v0/keyring`). The file store is used whenever the keyring cannot be, and the client logs which reason applied:

```text
Using file-based token storage because no D-Bus session bus detected
Using file-based token storage because a keyring timeout was recorded within the last …
Using file-based token storage because the caller requested it
Keyring LoadStoredToken timed out after %v, falling back to file storage
```

The changelog built into the binary explains the timeout rule: "the CLI now bypasses the keyring when no D-Bus session bus is present (headless hosts and containers), skips it for an hour after a timeout, and uses longer keyring timeouts so a slow-but-successful credential read is no longer cut short and forced into a fresh login."

The file is `~/.gemini/antigravity-cli/antigravity-oauth-token`. `(*cliFileTokenStorage).updateStoredToken` creates the directory and writes it with `os.WriteFile` mode `0600`. The contents are plain JSON, not encrypted:

```text
{"token": {"access_token": "[token]", "token_type": "Bearer", "refresh_token": "[token]",
           "expiry": "[time]", "expires_in": [seconds]},
 "auth_method": "consumer", "id_token_email": "[email]"}
```

Anyone who can read this file as your user, or a backup of it, can act as `agy` with all seven scopes, including `cloud-platform`, until the refresh token is revoked.

Evidence:
- Binary: `jetski/cli/backend/auth.NewCLITokenStorage`, `jetski/language_server/code_assist_client/codeassistclient.NewCompositeTokenStorage`, `jetski/language_server/code_assist_client/codeassistclient.shouldBypassKeyring`, `jetski/cli/backend/auth.(*cliFileTokenStorage).updateStoredToken` (`os.WriteFile(…, 0600)`)
- Binary: the log strings and changelog line quoted above

## A Gemini API key bypasses Cloud Code and its server switches

With `GEMINI_API_KEY` set, the header reads "Gemini API key". Model calls go to `generativelanguage.googleapis.com/v1beta/models/<model>:streamGenerateContent` through the Google Gen AI SDK, with the key in `x-goog-api-key` (`model_api/backends/geminiapi.NewClient`, `codeassistclient.(*GeminiAPIKeyAuthProvider).GetEndpointURL`). The main agent used `gemini-3.1-pro-preview` and titles used `gemini-3.1-flash-lite-preview`. No call goes to `daily-cloudcode-pa.googleapis.com`, so there is no account bootstrap, quota, server model list, `listExperiments` or trajectory upload. Clearcut telemetry to `play.googleapis.com/log` continues, and the first run still shows the terms screen with the data-use box ticked.

The switches come from the binary instead. `cli/store.FetchCLIFeatureProvider` checks the auth type (`CodeAssistClient.HasNoCCPAAccess`). For an API key, ADC or a gateway, it skips `listExperiments` and uses the built-in snapshot, adding the enterprise layer only for ADC. With a key, the default model is Gemini 3.1 Pro (Low), and `/model` lists Gemini 4 Neon, 4 Carbon, 4 Hydrogen, 4 Argon, 3.8 Flash, 3.7 Flash, 3.6 Flash and 3.1 Pro, with no Claude, GPT-OSS or Orchestrator entries. `/usage`, `/quota` and `/credits` have nothing to show. The changelog: "they are now left out of autocomplete and `/help`, running one explains why and points you to the key's usage in AI Studio".

Google's install page says that with an API key "the CLI never establishes an account session".

Evidence:
- Traffic: [model requests with an API key](../../resources/cli/protocol/wire-api-key.md)
- Binary: `jetski/cli/store.FetchCLIFeatureProvider`, `jetski/language_server/code_assist_client/codeassistclient.(*CodeAssistClient).HasNoCCPAAccess`, `jetski/model_api/backends/geminiapi.NewClient`, `jetski/cli/modelresolver.(*geminiAPIResolver).Resolve`
- Google: [Authenticate](https://antigravity.google/docs/install)

## The plan comes from `loadCodeAssist`

`loadCodeAssist` returns the account's tiers: `currentTier`, the tiers in `allowedTiers` (such as `free-tier` and `standard-tier`), and `paidTier`, the paid Google AI plan if there is one. `paidTier` carries an upgrade link and `availableCredits` (AI credit types and minimum amounts). The start screen shows the plan in brackets after the email. The response's `cloudaicompanionProject` becomes the `project` of every later call. `fetchAdminControls` returns `{}` for a consumer account. For business accounts it carries organisation policy that can lock permission presets and multi-agent features (`admincontrols.(*Manager).IsPermissionPresetLocked`, `overrideMultiagentControls`, `GetBoostCommandDisabled`).

Google's plans page gives every plan "Access to all product features, such as scheduled tasks and the CLI" and lists "Access to third-party models" for Ultra. It also says "Bring-your-own-key (BYOK) or bring-your-own-endpoint for additional rate limits" is not supported.

Evidence:
- Traffic: [`loadCodeAssist` and `fetchAdminControls` layouts](../../resources/cli/protocol/wire-account-and-config.md#loadcodeassist)
- Binary: `jetski/admin_controls/admincontrols.(*Manager).IsPermissionPresetLocked`, `jetski/admin_controls/admincontrols.(*Manager).overrideMultiagentControls`, `jetski/language_server_pb/language_server_go_proto.(*FetchAdminControlsResponse_FeatureControls_MultiagentControls).GetBoostCommandDisabled`
- Google: [Plans](https://antigravity.google/docs/plans/)

## The model list is sent by the server

`fetchAvailableModels` returns the models the account can pick, with display names, thinking levels, token limits, supported MIME types, per-model quota (`quotaInfo.remainingFraction`, `resetTime`) and per-model server switches (`modelExperiments`). For the AI Pro account it listed 39 entries. Beside the Gemini models, they included Claude Opus 5.5 and Claude Sonnet 5.5 (Low, Medium, High), GPT-OSS 120B (Medium), and "Orchestrator (Neon)" and "Orchestrator (Argon)". The default was `gemini-4-neon-high`. The plans page lists third-party models only for Ultra.

Evidence:
- Server: [available-models.json](../../resources/cli/server-switches/available-models.json)
- Traffic: [`fetchAvailableModels` layout](../../resources/cli/protocol/wire-account-and-config.md#fetchavailablemodels-342-kb)

## Quotas come from the server and are shown in `/usage`

`retrieveUserQuotaSummary` returns quota groups. Each group has buckets for a `weekly` and a `5h` window, each with `remainingFraction` and `resetTime`. The top-level description explains the model:

```text
Within each group, models share a weekly limit and a 5-hour limit. Quota is consumed
proportionally to the cost of the tokens. Thus, limits will last longer with shorter tasks
or using more cost-effective models. The 5-hour limit smooths out aggregate demand to fairly
distribute global capacity across all users, while your weekly limit is tied directly to
your individual tier.
```

For the AI Pro account, the groups were "Gemini Models" (Gemini 4 Neon, Gemini 4 Hydrogen, Orchestrator (Neon), Gemini 3.8 Flash and others), a group with the third-party models (Claude Sonnet 5.5, GPT-OSS 120B and others), and "Gemini 4 Argon", whose description says "For paid Pro plans, Argon quota is separate. Higher Argon quota (included as part of the larger Gemini quota bucket) is available on Ultra plans." Bucket descriptions read like "You have used some of your weekly limit, it will fully refresh in 6 days, 22 hours."

`/usage` (`commands.(*usageCommand)`) renders this as a plan section, one block per quota group and an AI credits section. The status line shows quota usage as well. The credits setting decides what happens when the base quota runs out. Google's plans page describes it as "**Never**" (wait for the refresh) or "**Always**" (spend AI credits). The 1.5.14 changelog says the "Use AI Credits" setting is no longer offered to accounts signed in through a Google Cloud project or ADC, "where it does not apply".

Evidence:
- Traffic: [`retrieveUserQuotaSummary` layout](../../resources/cli/protocol/wire-account-and-config.md#calls-that-send-only-the-project)
- Binary: `jetski/cli/backend.(*ServerBackend).RetrieveUserQuotaSummary`, `jetski/cli/commands.(*usageCommand).Execute`, `jetski/cli/model.UsageModel.renderPlanSection`, `jetski/cli/model.UsageModel.renderQuotaGroup`, `jetski/cli/model.UsageModel.renderCreditsSection`
- Google: [Plans](https://antigravity.google/docs/plans/)

## `/teamwork` and `/boost` depend on server switches, the sign-in method and the model

`cortex/core/components/config.buildDefaultAgent` fills the default agent's list of built-in agents from switches. It adds "teamwork" if `enable-teamwork-subagent` is on and "DeepCoder" (the agent behind `/boost`) if `enable-owl-slash-command` is on. `cortex/slashcommands.hasBuiltinAgents` offers `/teamwork` and `/boost` only when their agent is in that list. With a Google account, the switches come from `listExperiments`. Otherwise they come from the built-in snapshot, where both are missing from the base and CLI layers and on in every enterprise entry. The enterprise entries also set `disable-teamwork-forced-flash-model: true`. `cortex/config.TeamworkDefaultsToFlash` returns the opposite of that switch, so unless it is set, Teamwork's orchestrator runs on Flash whatever model you picked. Google's feature overview lists `/boost` and `/teamwork` as paid-plan features.

| Sign-in | Switch source | `/teamwork` | `/boost` | Teamwork orchestrator |
|---|---|---|---|---|
| Google account, AI Pro | Server: `enable-teamwork-subagent: true`, `enable-owl-slash-command: true`, `disable-teamwork-forced-flash-model: false` | Offered with Gemini 4 Neon, "No matches" with Gemini 4 Argon | Offered | Flash |
| ADC | Built-in enterprise layer | On | On | Your model |
| Gemini API key | Built-in base and CLI layers | "No matches" | "No matches" | — |
| Gateway | Built-in base and CLI layers | Off | Off | — |

With a key, typing `/teamwork build a tiny calculator` is sent to the model as plain text. `/swarm` is offered under every sign-in ("(Preview & Quota Heavy) Run a swarm…"). The same AI Pro response also sets `enable-orchestrator-model-selection: true`, `remote-control-setting-enabled: true`, `enable-voice-relay: true` and `enable-remote-personal-agents: false`. What the multi-agent commands do is in [agents-and-swarm.md](agents-and-swarm.md), and the other switches are in [server-switches.md](server-switches.md).

Evidence:
- Binary: `jetski/cortex/core/components/config.buildDefaultAgent`, `jetski/cortex/slashcommands.hasBuiltinAgents`, `jetski/cortex/config.TeamworkDefaultsToFlash`, `jetski/cortex_pb/cortex_go_proto.(*CustomizationConfig).GetEnableTeamworkSubagent`, `jetski/cli/store.FetchCLIFeatureProvider`
- Server: [`enable-teamwork-subagent`](../../resources/cli/server-switches/INDEX.md#enable-teamwork-subagent), [`enable-owl-slash-command`](../../resources/cli/server-switches/INDEX.md#enable-owl-slash-command), [`disable-teamwork-forced-flash-model`](../../resources/cli/server-switches/INDEX.md#disable-teamwork-forced-flash-model), [`enable-orchestrator-model-selection`](../../resources/cli/server-switches/INDEX.md#enable-orchestrator-model-selection), [`remote-control-setting-enabled`](../../resources/cli/server-switches/INDEX.md#remote-control-setting-enabled), [`enable-voice-relay`](../../resources/cli/server-switches/INDEX.md#enable-voice-relay), [`enable-remote-personal-agents`](../../resources/cli/server-switches/INDEX.md#enable-remote-personal-agents)
- Google: [Feature overview](https://antigravity.google/docs/features/)
