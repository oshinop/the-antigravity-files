# Antigravity 2.33.0 Electron shell: privacy and security assessment

## Scope

Static review of the Antigravity 2.33.0 (macOS arm64) Electron shell: `app.asar` sources, bundled `electron-updater` and `chrome-devtools-mcp`, `Info.plist`, `app-update.yml` and code-signing entitlements. Paths in code evidence are relative to `app.asar/dist/` unless prefixed otherwise.

The Electron shell is not the cloud inference implementation. Data reaching the language server may leave the machine afterwards; where that route isn't established, this document says so.

## Summary of security findings

| ID | Severity | Finding | Reachability / qualification |
|---|---|---|---|
| E-01 | High | Local Hub enables `<webview>` without a main-process attach policy fixing guest security preferences | Conditional renderer-compromise escalation; exact exploit is `[INFERENCE]`, not tested |
| E-02 | Medium | Updater explicitly setting its channel implicitly enables downgrades | Directly established by bundled dependency setter |
| E-03 | Medium | Guest permission handler grants all requests except four agent-control UI permissions | Directly established; OS TCC still applies |
| E-04 | Medium | UI/flags TLS trusts any localhost certificate, unlike pinned actuator transport | Needs local interception/port takeover for exploitation |
| E-05 | Medium | Login-shell environment is broadly inherited by the LS | Secret exposure surface, not proof of secret exfiltration |
| E-06 | Medium | Deep links and LS output, including authentication URLs, are written to local logs | Code paths established; actual log contents not accessed |
| E-07 | Medium | BeyondCorp proxy rewrite removes `xup.` directives and can fail open to DIRECT | Enterprise network-policy impact depends on deployed PAC |
| E-08 | Low | Several sensitive IPC handlers lack positive Hub/top-frame authorization | Defense-in-depth gap; arbitrary web pages do not automatically have IPC |
| E-09 | Low | Renderer can select permissive external-protocol policy without native confirmation | A compromised local Hub can launch non-denylisted OS handlers |
| E-10 | Low | Site / RP-ID boundaries rely on incomplete hand-maintained public-suffix tables | Boundary drift; no demonstrated credential theft |
| E-11 | Low | Broad ATS exception and permissive Plugin helper entitlements increase platform attack surface | Not an independently demonstrated exploit |

No Critical finding or unauthenticated remote code execution is established by this audit.

## 1. BrowserWindow, WebContentsView, webview, preload, IPC

### Window preferences and trust boundaries

* Local Hub: `utils.js:627-640` sets `nodeIntegration:false`, `contextIsolation:true`, a bundled `preload.js`, debug-console-gated devtools, and `webviewTag:builtInBrowser`. It does not explicitly set `sandbox` or `webSecurity`; `[INFERENCE]` contemporary Electron defaults make the Node-disabled renderer sandboxed and enforce web security. These defaults should not be represented as an explicit hardening guarantee.
* Hub can be a `BaseWindow` hosting a privileged `WebContentsView` (`utils.js:648-676`), not just a BrowserWindow. Authorization needs to resolve the Hub's actual webContents, not assume BrowserWindow lookup is sufficient.
* Built-in WCV browser guests explicitly use `sandbox:true`, `contextIsolation:true`, `nodeIntegration:false`, `webviewTag:false`, `navigateOnDragDrop:false`, caller-selected partition, and optional WebMCP Blink feature (`wcvBrowserRegistry.js:670-685`). `webSecurity` is not disabled.
* Browser popup overrides explicitly set sandbox and isolation, disable all Node integration variants and webview, fix the guest session, disable drag/drop navigation, fullscreen, kiosk, always-on-top and related UI attributes (`webviewBrowser.js:2788-2820`). Browser navigation and popup policy runs before opening (`webviewBrowser.js:2667-2725`).
* The catcher is sandboxed/isolated/Node-disabled; curtain JS is disabled (`utils.js:660-691`). Script jail uses a separate session and stronger isolation (section 4).
* SSH windows explicitly reject `will-attach-webview` (`remote/remoteWindowSecurity.js:318-327`). This control is notably absent on the local Hub path.

### E-01 — High: no local main-process webview attach hardening

**Evidence:** `utils.js:627-636` enables `webviewTag` for the local Hub. `main.js:320-337` configures webview contents after their creation. A search of all distributed JS found `will-attach-webview` only in the SSH denial above; there is no local `will-attach-webview` handler stripping preload paths or forcing Node/sandbox/web-security attributes. WCV guests and native popup overrides are hardened, but those do not govern a renderer-created `<webview>`.

**Impact:** `[INFERENCE]` JavaScript execution in the privileged Hub renderer (for example a Hub XSS) can create a webview with attacker-selected guest attributes/preload rather than remaining limited to the contextBridge API. Electron's webview configuration surface can request Node-enabled or otherwise weakened guests; the exact accepted combination in this packaged Electron build was not exercised. This is a conditional renderer-compromise-to-native escalation risk, not proof that browsing an arbitrary external page currently executes native code.

**Remediation:** On every local privileged host webContents, reject unexpected guests and force `nodeIntegration:false`, all Node worker/subframe variants false, `contextIsolation:true`, `sandbox:true`, `webSecurity:true`; discard renderer-provided preloads and constrain partitions/navigation. Prefer exclusive WCV ownership if webview support can be removed.

### Preload exposed APIs

`preload.js:12-300,301-535` exposes fixed named methods via contextBridge, not a generic `ipcRenderer` object. The description “minimal” in its header is misleading: local Hub capability is broad. Exposures include:

* `electronUpdater`: status, checks, apply, quit/install.
* `dialog`: single/multiple directory pickers.
* `nativeStorage`: all items, update arbitrary keys, change subscriptions.
* `logs`: read Electron log contents.
* `extensions`: set local sidecar authority map.
* `deepLink`: subscribe and consume stored URL, potentially containing auth code.
* `nativeNotifications`, agent count, window controls, zoom, OS external links and file reveal.
* `electronNative`: browser creation/navigation/input/capture, isolated-world/frame JS, network request history, downloads/uploads, browser certificate exception and site-data clearing, device chooser, page prompts, dialogs, WebMCP tools, script jail.
* `desktopMic`, `sshRemote`, `ide`, `wsl`, and `agyComputerPreview`.

SSH preload omits local files/browser/script-jail/updater/log/deep-link capabilities, and narrows other APIs (`preload.js:475-529`). Main-process checks remain necessary; absence from a bridge is not a substitute for sender authorization.

### IPC authorization

Strong examples: WCV verifies a local Hub main-frame owner and tab ownership (`wcvIpcHandlers.js:75-145`); computer preview requires exact main frame, allowed local Hub viewer, and session-to-webContents ownership (`computer/previewIpc.js:26-44,54-112`); jail verifies active Hub identity and rejects subframes (`scriptJail.js:267-285`); remote windows have dedicated backend/storage scopes (`ipcHandlers.js:163-176`).

### E-08 — Low: inconsistent positive sender validation

**Evidence:** `ipcHandlers.js:115-121`'s `localOnly` rejects remote contents but does not establish that the sender is a local Hub/main frame. Asynchronous `storage:get-items` and `storage:update-items` (`437-441`) lack the positive identity check used by the synchronous storage handler (`431-435`). Local authority-map update accepts every `Object.entries(authorities)` value unchanged (`457-474`). Logs/updater/dialog wrappers and `main.js:391-399`'s stored-deep-link handler similarly rely on “not remote” rather than “known local Hub top frame.”

**Impact:** `[INFERENCE]` An unintended IPC-capable local contents/preload can read/write app state, read logs or consume an auth link. An arbitrary Node-disabled guest has no general IPC bridge merely because the handlers exist; this audit does not claim a direct website-to-IPC exploit.

**Remediation:** Reuse the strict Hub-main-frame predicate for all privileged channels; then validate payloads. Keep explicit guest-only channels separate.

## 2. Deep links, custom schemes, redirects, OS URLs

* `Info.plist` registers `antigravity`. `main.js:187-239` forwards deep links only to local windows and registers the OS protocol; open-url also calls this handler (`302-305`). Command-line links must start with the configured protocol (`240-258,350-356`). Deep links take precedence over SSH-host arguments (`285-290`), reducing protocol-triggered SSH connection risk.
* The shell forwards the URL rather than parsing/validating all route payloads. `main.js:194-195` recognizes OAuth success only for a flags refresh; the renderer/LS is responsible for verifying an auth code/state. This inspection cannot certify local OAuth state/PKCE enforcement from the shell alone.
* `customScheme.js` handles `plugin://`, not `antigravity://`. `plugin` is a standard, secure, fetch/CORS/service-worker-enabled scheme (`15-27`). A renderer-provided authority maps to a sidecar upstream (`68-86`). Appending the path to the upstream origin and checking `targetUrl.host` prevents `//host` path authority switching.
* `redirects.js:48-115` proxies through Electron's main-process network/session cookies, using `credentials:include` when Origin is absent and `same-origin` otherwise. Redirects are manual. `pluginResponse` returns 502 for cross-origin redirects and rewrites same-origin redirects back into the plugin authority (`123-145`). The upstream URL itself is not restricted to loopback by `extensions:send-authorities`; its current caller is trusted Hub code, not an independent low-privilege trust domain.
* Cached `agy-favicon://<token>/` serves only existing cached bytes, GET/root path, no remote fetch, with nosniff, no-store and `default-src 'none'; sandbox` CSP (`customScheme.js:37-64`).
* Automatic Hub outside navigation uses the `unconfirmed` policy (`utils.js:565-569`), allowing only HTTP, HTTPS, mailto. Denied protocols include file, javascript, data, shell, applescript, SMB and numerous Office/Windows handler schemes (`externalUrl.js:19-71`). Errors log scheme/type, not whole URLs (`107-121`).

### E-09 — Low: “confirmed” is only a caller-selected policy

**Evidence:** `ipcHandlers.js:636-647` routes local renderer `openExternal` directly to `openExternalSafely(url,'confirmed')`, without native confirmation or user-activation verification. `externalUrl.js:83-92` accepts every syntactically valid non-denylisted multi-character scheme under this policy. It does not mean a native prompt actually confirmed the action.

**Impact:** `[INFERENCE]` A compromised Hub renderer can invoke additional installed OS protocol handlers. No bypass of the explicit denied schemes is shown; untrusted guest automatic navigation uses a different policy.

**Remediation:** Allowlist needed application protocols; require a trusted native prompt for other protocols, bound to the exact normalized URL.

## 3. Language-server launch, environment, ports, tokens, TLS

* Packaged LS is fixed under `process.resourcesPath/bin/language_server`; development override is not used when packaged (`languageServer.js:82-87`). Shell prefetch starts before Electron ready (`main.js:123-125`).
* `lsDefaults.js:8-29`: `--standalone`, IDE/user-agent `antigravity`, subclient `hub`, app version, `--https_server_port`, `--csrf_token`, `--app_data_dir`, `--api_server_url=https://generativelanguage.googleapis.com`, `--cloud_code_endpoint=https://daily-cloudcode-pa.googleapis.com`. `languageServer.js:217-248` adds `--enable_sidecars`, optional host-bridge URL/token and headless; macOS spawns directly, not via a shell string. These arguments include bearer-like secrets visible to sufficiently privileged local process inspection.
* Default port is dynamically assigned; `JETSKI_LS_PORT` can override it. Startup parses the port from LS stdout/stderr and waits at most 60 seconds (`languageServer.js:109-115,217-364`; `main.js:511-541`).
* Main creates a UUID CSRF token (`main.js:493`) and separate 32-byte random host-bridge token. Host bridge is HTTP on ephemeral `127.0.0.1`, validates socket peer and exact Host header, then constant-time bearer comparison (`hostBridgeServer.js:55-153,160-196`). CSRF token does not authorize ApplyUpdate on this separate bridge.
* LS receives the node wrapper/module path, plus `CHROME_DEVTOOLS_MCP_JS`. Debug CDP active-port path is forwarded only if Electron was explicitly started with remote-debugging-port; otherwise related env values are removed (`languageServer.js:253-275`).

### E-05 — Medium: broad shell environment inheritance

**Evidence:** `loginShellEnv.js:55-57,89-111,180-195` executes the user's selected shell interactively as a login shell (`-ilc`, csh/tcsh variant), including its rc files, then runs `/usr/bin/env`. Returned variables are filtered only against shell state and four injected control names (`loginShellPolicy.js:14-36`). `languageServer.js:251-252` merges `{...process.env,...loginShellEnv}` into the LS child environment. There is no secret-name allowlist or credential filtering. A 60-second aggregate read budget and 1 MiB output bound exist.

**Impact:** `[INFERENCE]` API keys, cloud credentials, proxy credentials, `SSH_AUTH_SOCK`, and any other exported secrets become accessible to the LS and descendants that inherit the environment. Running login rc files may execute arbitrary user-configured shell commands as part of launching the app. This is deliberate tool-discovery behavior, not evidence of a malicious collection endpoint.

**Remediation:** Define a minimal required environment contract; make broad inheritance explicit/opt-in and document downstream process access. Do not log env values.

### E-04 — Medium: inconsistent loopback server authentication

**Evidence:** `languageServer.js:475-483` accepts **any certificate** for hostname `127.0.0.1` or `localhost` in Electron defaultSession, not only the active LS port/cert. `mendelFlagSync.js:111-131` sends the admin CSRF token to loopback with `rejectUnauthorized:false` and no pin.

By contrast, `actuator/nodeTransport.js:65-108,111-125,190-226` uses only bundled LS certificate trust anchors and checks authorized state or an exact fingerprint for expiry/not-yet-valid exceptions before allowing any HTTP/2 traffic/CSRF header. Its `rejectUnauthorized:false` is paired with manual verification, and must **not** be reported as unconditional insecure TLS. Current pin is in `actuator/pinnedLsCert.js:11-39` (localhost/127.0.0.1, certificate valid 2026-09-08 to 2027-03-26; previous pin absent).

**Impact:** `[INFERENCE]` An attacker able to replace/intercept a local listener, including a restart/port-reuse scenario, could impersonate the LS to flags polling or the default-session UI and capture the admin token. This is not ordinary Internet MITM exposure: the destination is loopback. Broad default-session localhost trust also affects non-LS services using that session.

**Remediation:** Apply the actuator's pinning consistently, bind trust to active LS identity/port, and rotate per-install identities if stronger local isolation is desired.

## 4. Computer use, native helper, screenshots/AX, script jails

* macOS 14+ only in shell wiring (`computer/index.js:80-88`). Startup requires persisted `enable-agy-hub-cua-macos:true` override, or unpackaged-only `AGY_ENABLE_CUA=1`. Source explicitly calls the renderer-writable override a convenience gate, not a security boundary (`computer/gate.js:5-14,90-95`).
* Helper is bundled `agy-desktop-helper`; it is lazily launched as `serve`, with piped stdin/stdout and separate frame/media pipe (`computer/desktopHelper.js:123-155`). Production helper/RTC environment allowlist is only PATH, HOME, TMPDIR, LANG (`computer/gate.js:113-135`; `computer/index.js:235-241`). This is more restrictive than LS inheritance.
* Required OS grants: Accessibility and Screen Recording (`computer/index.js:115-145`). Permission onboarding reads grants passively and only opens named macOS settings panes. Native TCC remains meaningful; app entitlements are not themselves evidence that the user granted access.
* Driver provides foreground click/move/drag/scroll/type/key combos/focus; background app binding, AX snapshots, AX press/set-value/select/scroll, element clicks and window screenshots (`computer/macComputerDriver.js:10-24,127-172,393-440,498-515`). Each policy-carrying operation includes allowed bundle IDs and optional blocked-domain policy (`127-140`).
* Main trusts the LS to provide consented/locked app sets. Missing `allowedBundleIds` yields an empty set, not unrestricted access (`computerActuator/computerActuatorHandler.js:1298-1314`). Per-app policy and screenshot redaction are delegated to the native helper; this audit inspected their call protocol, not independently verified the Swift enforcement implementation.
* Screenshot PNG bytes, dimensions, geometry, focused app/window metadata, browser URL/verdict reports and AX snapshot text are returned over the actuator to the local LS (`computerActuator/computerActuatorHandler.js:1745-1819,1208-1217`). `[INFERENCE]` LS/model workflows may transmit these onward; the shell does not name that cloud endpoint in this code path.
* User takeover/paused/stopped/abandoned paths fence actions and suppress captures; HUD stop and per-agent stops are wired (`computer/index.js:259-286`; `computerActuator/computerActuatorHandler.js:1777-1796,1883-1905`). Pause persists; kill switch is `/tmp/agy_desktop_endpoint_off` checked every two seconds; actuator identity initially hashes hostname to 8 hex characters then persists (`desktopHost/config.js:47-80,83-121`). The identity is pseudonymous and stable, not an anonymous random session ID.

### Two distinct script isolation systems

1. **Browser script jail:** separate nonpersistent session, blocks all webRequest traffic, denies permission requests/checks, blackhole proxy `http://127.0.0.1:9`, disables non-proxied WebRTC UDP, window opening and navigation; strips all session preloads and fails if any remain (`scriptJail.js:289-349,554-582`). Runs model code in a dedicated Worker and brokers a MessagePort to the requesting Hub. Only active Hub senders pass (`267-285`).
2. **Native computer script:** fresh `agy-desktop-script` child, no args and **empty env** (`computerActuator/scriptRunner.js:297`), supervised stdio RPC, 30-second default/120-second maximum, 256 MiB RSS watchdog, bounded output (`75-94`). Mediator allowlist includes only computer operations, not arbitrary shell/files/network; foreground declaration, consented/locked app membership, op/screenshot budgets, and all normal driver fences apply (`scriptMediator.js:50-86,322-446`). Native Seatbelt confinement is **not proven by JS comments alone**. The host advertises script support only after hashing the binary and running its sandbox selftest successfully (`desktopHost/host.js:307-318,366-378`; `desktopHost/scriptSelftest.js:58-98,142-161`). **That selftest was not run during this audit.**

### Observed signing entitlements (metadata, not permission grants)

`codesign -d --entitlements - <target>` returned:

| Signed target | Entitlements |
|---|---|
| `Antigravity.app` | apple-events automation, audio-input, camera, allow-jit |
| Resources/bin `agy-desktop-helper` | empty dictionary |
| Resources/bin `agy-desktop-script` | empty dictionary |
| Resources/bin `cable_ble_scanner` | allow-jit |
| Frameworks `Antigravity Helper.app` | allow-jit |
| Frameworks `Antigravity Helper (Renderer).app` | allow-jit |
| Frameworks `Antigravity Helper (GPU).app` | allow-jit |
| Frameworks `Antigravity Helper (Plugin).app` | allow-jit, allow-unsigned-executable-memory, disable-library-validation |

The main app's entitlements must not be incorrectly attributed to the Resources/bin desktop helper: this artifact's desktop helper has an empty entitlement dictionary. No `com.apple.security.app-sandbox` entitlement was observed. Runtime Seatbelt/Chromium sandboxing is a separate mechanism.

## 5. desktopHost remote viewer, pairing, WebRTC

* Local preview signaling is IPC, limited to the exact local Hub main frame with per-webContents session ownership and cleanup on destruction (`computer/previewIpc.js:26-112`). Process-wide loopback WebRTC enablement is paired with a webContents policy allowlist (`computer/previewViewers.js:15-26,76-90`).
* `desktopHost/rtcSender.js:14-25,68-118,191-237`: local sender gathers/filters **only loopback** ICE candidates, no STUN/TURN, rejects SDP without a retained loopback candidate. Four sessions max, local TTL 60 seconds; renew required. Remote answer filters raw host IP candidates, retaining mDNS/srflx/relay. Remote views contain only that root conversation's agent windows, never whole-display mode.
* Remote signaling is `computer_viewer` actuator through the LS/ComputerViewerService; media is peer-to-peer or TURN, not through the LS (`desktopHost/viewerHandler.js:41-55`). **No fixed signaling/STUN/TURN hostname exists in desktopHost sources.** ICE URLs/username/credential come from the offer (`viewerHandler.js:218-228`), so statically enumerating all possible remote media destinations is impossible.
* Offers require nonexpired deadline, session ID/SDP size constraints, exactly one SHA-256 DTLS fingerprint, ECDSA P-256 proof over `agy-computer-view-v1\n<nonce>\n<root>\n<fingerprint>`, replay check, and a currently running agent for the root, all before pairing or media (`viewerHandler.js:166-215`; `pairing.js:45-136`). First pairing calls a native local prompt with device label and shortened fingerprint; non-allow/timeout rejects (`viewerHandler.js:260-286`). Paired device hash/label/time persist atomically in mode-0600 JSON under mode-0700 directory (`pairing.js:174-239`); revocation closes sessions.
* Remote sessions end at expiry (+2 seconds), close, or when the root has no running agent. Renew/close are accepted via the LS relay after session lookup; the offer proof does not make the LS an untrusted anonymous signaling relay for these later operations.
* DataChannel supports bounded, parsed control messages and metadata; it is not a general OS-command channel (`rtcSender.js:150-179`; `desktopHost/protocol.js` parser). There is more than just video: app/window identifiers, agent/activity/frame metadata cross it.
* **Packaged reachability matters:** `computer/index.js:70-73` strips all `AGY_*` through `cuaEnv(...,app.isPackaged)`, whereas `remoteViewEnabled` requires `AGY_DESKTOP_REMOTE_VIEW==='1'` (`209-215`). Thus the normal packaged Hub path leaves remote viewing **off** and does not expose a working environment-variable toggle for it. Standalone desktopHost/unpackaged wiring can enable it. Do not describe this shipping app as an always-on remote desktop server.

## 6. Browser agent, CDP, network capture, WebAuthn, BeyondCorp

### Browser engines and profiles

`browserEngine/chromeDiscovery.js:46-98` checks fixed official stable Chrome executable locations (macOS `/Applications/Google Chrome.app/...` and `~/Applications/...`) for executable access, not publisher/signature verification. `browserEngine/browserEngine.js:80-123,155-181` uses dedicated `browser-profiles/<validated-id>`, `--headless=new`, `--remote-debugging-pipe` on fds 3/4, `--disable-blink-features=AutomationControlled`, no first run/default browser checks. There is **no TCP CDP listener in this path**. The user's everyday default Chrome profile is not selected. Persistent agent profiles nevertheless retain browsing/account data. The Linux-only headless no-keyring path uses `--password-store=basic`; it is not a macOS finding.

Electron's own remote-debugging endpoint is a separate explicit startup option. `paths.js:81-118` best-effort tightens userData to 0700 and `DevToolsActivePort` to 0600; `main.js:345-349` removes stale active-port file when disabled. This is not proof that CDP is enabled by default.

### Network capture and sanitization

* `guestNetworkController.js:9-11,64-112,250-405`: CDP network tracker holds at most 50 entries per guest/128 in flight; sanitized URL, method, resource type, status, safe headers and up to 2048 characters of textual XHR/fetch/error response preview are forwarded to the Hub renderer.
* Protected IdPs, token endpoints, metadata/link-local services, corporate SSO and sensitive cross-origin Google services are suppressed, including redirect tombstones. Generic credential field/header names, Bearer/Basic/Google token/JWT forms, control/format characters and untrusted-content boundary tags are scrubbed (`guestNetworkSanitizer.js:22-211,254-303,307-407`). This is heuristic redaction, not a guarantee against arbitrary secrets in a response body/path/unknown field name.
* Detailed headers/body require same origin or exact CORS origin echo; wildcard ACAO is intentionally insufficient (`guestNetworkSanitizer.js:423-443`). Request bodies are not part of the emitted network entry constructed at `guestNetworkController.js:292-308`.
* Agent-owned cross-site subframe requests strip Cookie/Authorization and response Set-Cookie; proxy authorization is retained (`guestCrossSiteCredentials.js:326-415`; `webviewBrowser.js:1477,2264-2265`). This is not a general top-level credential purge; normal authenticated agent browsing remains possible.

### E-03 — Medium: browser site permissions are granted by default

**Evidence:** `guestPermissionPolicy.js:16-21,34-57` denies only fullscreen/automatic-fullscreen/pointerLock/keyboardLock during active agent control and otherwise calls `callback(true)`. `webviewBrowser.js:2264` installs it on guest sessions. This includes non-agent-controlled guests and unrelated permission types.

**Impact:** `[INFERENCE]` Sites can obtain Electron-level permissions (for example media/location/notification requests) without a browser-origin consent prompt from this handler, subject to Chromium rules, device-specific handlers and macOS TCC. Existing app-wide camera/microphone grants would not constitute user consent for every web origin. This is broader than merely preventing agent UI spoofing.

**Remediation:** Default-deny unneeded permissions; prompt per origin for needed sensitive capabilities, and deny them during agent control unless explicitly authorized.

### WebAuthn / caBLE

* No macOS Touch ID/Secure Enclave configuration: source explains missing restricted keychain-access-groups/provisioning support (`webauthn.js:77-94`). Native account chooser handles multiple credentials, while one credential is auto-selected (`204-249`).
* Top-frame browser guests only for custom caBLE assertion; nonopaque origin, secure origin/RP suffix relation, visibility, WCV user-controlled state and focused-or-recent-input checks, one ceremony per sender; navigation/destruction abort (`cablev2.js:1923-2031`). Focus alone satisfies the focus/recent-input disjunction; it is not a requirement for a fresh click on every ceremony.
* BLE scanner matches ephemeral advertisement data; QR uses fresh P-256 identity/secret, then Noise-derived authenticated encryption and CTAP assertion over `wss://<tunnel-domain>/cable/connect/<routing>/<tunnel>`. Assigned domains are `cable.ua5v.com`, `cable.auth.com`; protocol can derive other domains (`cablev2.js:92-98,435-473,566-573,1695-1815`). HTTPS validates tunnel TLS; RP hash/User Presence/User Verification are checked (`1000-1019,1079-1101`). Scanner process and network/modal are terminated on timeout/cancel (`1695-1754`). The phone keeps credential private keys; this shell handles assertion material and public credential identifiers, not evidence of private-key extraction.

### E-10 — Low: approximated public-suffix boundaries

**Evidence:** `guestCrossSiteCredentials.js:24-174,183-228` uses custom ccTLD/shared-host suffix sets, not the full PSL. `cablev2.js:943-998` similarly checks only a finite multi-label suffix set and selected ccTLD patterns before accepting an RP-ID suffix. These are security decisions that change when public/private registry boundaries change.

**Impact:** `[INFERENCE]` Unlisted shared-hosting/public suffixes can be treated as one site for credential withholding, or accepted as RP IDs contrary to full WebAuthn registry checks. Existing credentials scoped to a victim-specific RP ID do not automatically become usable on a sibling tenant; this is not a demonstrated passkey-account takeover.

**Remediation:** Use a maintained PSL implementation, including its private section, and conformance cases for multi-tenant domains.

### E-07 — Medium: BeyondCorp PAC rewrite can bypass a required proxy

**Evidence:** `beyondcorpPac.js:139-179` reads a BeyondCorp extension PAC from installed Chrome profiles; `180-218` wraps `FindProxyForURL`, strips all `HTTPS/PROXY ...xup.` entries and returns `'DIRECT'` when no non-xup entries remain. Comments explicitly describe avoiding proxy-auth 407 failures because Electron lacks the Chrome extension listener.

**Impact:** `[INFERENCE]` Corporate policy routing through an XUP proxy can silently become direct egress, bypassing proxy-mediated controls/auditing or changing which networks receive traffic. Other proxy fallbacks are preserved; no claim is made that this bypass succeeds against external network/firewall enforcement.

**Remediation:** Make the enterprise-policy change explicit and administrator-controlled; default to failure rather than DIRECT when an authenticated required proxy cannot be supported.

### Bundled chrome-devtools-mcp

The shell forcibly disables MCP config auto-discovery, usage statistics and update checks (`languageServer.js:268-275`). Bundle honors the env gates (`MCP/config/ConfigLocator.js:63-67`, `config/ServerConfig.js:147-152`, `utils/check-for-updates.js:23`); telemetry initializes only when resolved usageStatistics is true (`MCP/index.js:110-123`). Therefore finding a Clearcut implementation in the dependency is **not evidence that normal Hub-spawned MCP sends telemetry**. Its dormant default destination is `https://play.googleapis.com/log?format=json_proto` (`MCP/telemetry/watchdog/ClearcutSender.js:9`). CLI-mode config defaults can remove filesystem-root restriction when `viaCli` and unrestricted paths are not explicitly disabled (`MCP/config/ServerConfig.js:110-134`); the shell does not show the LS's final MCP invocation, so its active filesystem scope cannot be established here.

## 7. Telemetry, flags, logs, crashes

* `mendelFlagSync.js:42-55,111-131,182-255`: POST `{}` to local `/exa.language_server_pb.LanguageServerService/GetMendelFlags`, authenticated by CSRF, at startup/port changes/refresh and every 60 seconds, 15-second timeout, 1 MiB maximum response. Persists the WCV-three-layer flag in app storage. The shell poll is local; LS upstream experiments/configs traffic is outside this module.
* `main.js:338-344` initializes electron-log only for default session, not future guest partitions, then replaces global console with logger functions. `paths.js:140-142` places LS log in Electron app logs dir; `main.js:573` prints Electron log's resolved path at runtime. This audit did not read actual user logs or infer a configured remote electron-log sink.
* No `crashReporter.start`, `submitURL`, Sentry, Statsig, Clearcut implementation or safeStorage/keytar API call was found in distributed shell JS (Clearcut exists in separately bundled MCP as above). This is a bounded negative result, not a guarantee the Chromium/LS/dependencies have no reporting behavior.
* LS restart monitor buffers ~100,000 characters of stderr, extracts crash stack and logs it, restarts up to three times per minute (`languageServer.js:73,282-301,383-431`). It does not upload a crash itself.
* macOS run-in-background defaults true (`services/settingsService.js:13-20`); closing all windows need not end LS or background jobs (`main.js:674-685`). Quit is the relevant process-lifetime boundary.

### E-06 — Medium: authentication-bearing data can persist in local logs

**Evidence:** `main.js:354-356` logs the complete launch deep link (`'Launched with deep link:',deepLinkFromArg`), while `main.js:197` explicitly warns links can carry auth codes. `languageServer.js:310-332` writes every combined stdout/stderr line to the LS log before parsing OAuth/open-URL lines; `333-344` prints full auth/open URLs to console/electron-log. Crash stack text also reaches logs. `ipcHandlers.js:444-452` exposes all Electron log contents to the local renderer.

**Impact:** `[INFERENCE]` Auth codes, state, provider/client identifiers and token-bearing URLs printed by the LS can survive in logs, backups or support bundles; this does not prove long-lived tokens were actually emitted. Local metadata, paths and tool diagnostics may also persist. No retention/redaction guarantee is apparent in these application call sites.

**Remediation:** Redact deep-link query/fragment and LS auth URL parameters before logging; separate auth signaling from arbitrary text logs and explicitly define support-export filtering and retention.

## 8. Updater, integrity, signing, rollback

* `Contents/Resources/app-update.yml:1-3`: generic HTTPS provider at `https://antigravity-hub-auto-updater-974169037036.us-central1.run.app/manifest/`; cache `com.google.antigravity`.
* `updater.js:66-70,163-174,242-253`: initial automatic check after 10 seconds, then hourly; architecture channel (`latest-arm64` on this macOS), `x-app-version` request header, autoDownload true, autoInstallOnAppQuit when packaged. Automatic checking default true but user setting can disable it (`services/settingsService.js:20`; `updater.js:242-253`). Manual checks remain.
* Dependency persists `.updaterId` and sends stable pseudonymous `x-user-staging-id` on update metadata requests (`node_modules/electron-updater/out/AppUpdater.js:380-388,501-525`). This is a separate identity disclosure from the visible app-version header. Transport/server also sees IP and normal HTTP metadata `[INFERENCE]`.
* Manifest checksums flow into download integrity (`AppUpdater.js:559-566`), cached update SHA512 is recomputed (`DownloadedUpdateHelper.js:113-126`), and MacUpdater selects ZIP with architecture filtering then passes a localhost authenticated feed to Electron native `autoUpdater` / Squirrel.Mac (`MacUpdater.js:80-121,143-226`). Feed listener binds `127.0.0.1`; random basic auth and random ZIP path are used.
* The shell does not disable signature validation or implement its own macOS signature verifier. `[INFERENCE]` Native Squirrel.Mac is the platform code-signature enforcement point; this audit did not reverse-engineer/execute that native framework, so matching publisher enforcement on downloaded updates is not independently proven. The shipped app has signed-bundle ASAR SHA256 metadata in Info.plist; this alone does not establish runtime Electron fuse enforcement.

### E-02 — Medium: app channel assignment silently permits rollback

**Evidence:** `updater.js:163-171` sets `autoUpdater.channel`. Bundled `node_modules/electron-updater/out/AppUpdater.js:29-44` documents and executes `this.allowDowngrade=true` in that setter. App code does not reset it. `AppUpdater.js:338-362` permits an older semver when that flag is true.

**Impact:** A provider-controlled/replayed signed older release can be accepted as an update, undermining a monotonic security-version policy. Exploitation still needs control of a trusted update response/path or equivalent local state; network HTTPS and code signing are not thereby bypassed. Same-origin SHA512 does not itself prevent a malicious/replayed manifest choosing a legitimately signed old build.

**Remediation:** Set `allowDowngrade=false` **after** channel assignment; for stronger rollback resistance enforce signed metadata freshness/security-version floors, with explicit administrator recovery flow.

## 9. Login, token handoff, credential storage

* Local shell primarily launches LS and forwards auth/deep-link signaling. It opens validated HTTP(S) URLs printed in `ANTIGRAVITY_OPEN_URL` lines, and prints accounts.google.com auth URLs (`languageServer.js:112-123,333-344`). Local Hub outside navigation uses OS browser, not a dedicated embedded OAuth BrowserWindow in inspected main sources. Browser WebAuthn QR and account chooser windows are not Google OAuth token-storage windows.
* OS-returned `antigravity://...` payload is delivered to local renderer via deepLink subscription or one-use stored link (`main.js:187-229,391-399`); no token exchange/storage logic in this shell is established. The renderer/LS owns validation/exchange; this audit cannot certify OAuth state binding in that downstream code.
* For SSH sign-in, shell has substantially stricter controls: `remote/signInUrls.js:18-60,88-166` pins auth origins/paths, OAuth client IDs, exact scope sets, consent prompt, offline code flow and S256 PKCE with unprivileged loopback callback. Additional plugin/OAuth sign-ins are separately classified with anti-duplicate/encoding/request-object checks (`198-263`). WIF provider can select a workforce pool and requires user agreement. These remote checks must not be projected onto the local renderer flow without evidence.
* Observed personal OAuth client ID: `1071006060591-tmhssin2h21lcre235vtolojh4g403ep.apps.googleusercontent.com`; business: `884354919052-36trc1jjb3tguiac32ov6cod268c5blh.apps.googleusercontent.com`; WIF client ID `a2a6f0e3e-3ff0-462b-869a-47277df4c806`. Client IDs are public identifiers, **not client secrets**.
* `storage.js:106-126,208-251` stores arbitrary renderer values as plaintext JSON with temporary-file/rename and corrupt-file backup. `paths.js:72-78` places it in userData `app_storage.json`; `.gemini/<appname>` is LS app-data root, and `.gemini/config/config.json` supplies UI theme (`paths.js:54-70`). This audit did not access those user paths. No safeStorage/keychain encryption is implemented in StorageManager. **No evidence establishes that OAuth access/refresh tokens are actually stored in this UI JSON**, so a plaintext OAuth-token-storage finding would be unjustified from this source alone.

### E-11 — Low: broad platform exceptions

`Info.plist`'s `NSAppTransportSecurity` sets `NSAllowsArbitraryLoads:true`, with additional loopback HTTP/TLS1.0 exceptions. Plugin helper entitlements allow unsigned executable memory and disable library validation (metadata table above). `[INFERENCE]` These weaken platform defense-in-depth in code paths governed by those mechanisms. They do not imply all Chromium HTTPS certificate verification is disabled, or that the app can evade macOS TCC. Narrow ATS exceptions and determine whether the Plugin helper needs these entitlements.

## Privacy data inventory

“Unknown” means not determined from shell source, not that an opt-out exists. Hostnames in deny/allow lists are not automatically collection destinations.

| Data element | Source | Destination / storage | Trigger | Opt-out / control | Evidence |
|---|---|---|---|---|---|
| App version, OS/architecture channel | Electron app/runtime | Hub updater HTTPS `/manifest/` provider | Startup after 10s, hourly/manual checks | autoCheckForUpdates false; manual remains | updater.js:163-174,242-253 |
| Stable update staging UUID | userData `.updaterId`, generated random bytes | Hub updater, header `x-user-staging-id` | Update metadata check | No separate ID toggle in shell; disabling automatic checks reduces automatic disclosure | dependency AppUpdater.js:380-388,501-525 |
| Shell env, exported secrets, agent socket/proxy paths | Interactive login shell + app process env | Local LS process environment; inheriting children `[INFERENCE]` | Startup/restart | No production allowlist control shown for LS | languageServer.js:217-275; loginShellEnv.js:180-195 |
| CSRF admin token | crypto.randomUUID | Local LS argv, actuator headers, flags HTTPS; local caller visibility | Startup/RPC/poll | No feature opt-out; scope transport correctly | main.js:493; lsDefaults.js:21-22; mendelFlagSync.js:126-130 |
| Host bridge bearer | 32 random bytes | LS argv; local 127.0.0.1 bridge Authorization | Startup/update RPC | Separate from renderer CSRF | hostBridgeServer.js:160-196 |
| Auth/deep-link code/state/URLs | OS links / LS text output | Local Hub renderer, LS/Electron logs | Auth/open-link/startup flows | No logging-specific opt-out identified | main.js:197,354-356; languageServer.js:310-344 |
| OAuth account permissions | Login requests | accounts.google.com; auth.cloud.google | User login | Do not log in/revoke OAuth grant externally; exact local UI behavior outside this module | remote/signInUrls.js:18-60 |
| Cloud-platform, email/profile, cclog, experiments/configs, personal aicode scopes | Pinned remote LS clients | Google OAuth authorization endpoint | SSH LS sign-in | User/host authorization; not independently minimal privilege | remote/signInUrls.js:18-60 |
| App/UI settings, flags, arbitrary storage values | Renderer and Mendel result | userData `app_storage.json`, change events to scoped windows | UI changes/60s flag polling | Per-feature settings; no generic encryption | storage.js:208-267; mendelFlagSync.js:182-255 |
| Screenshots, pixels, window/app metadata, browser host/verdicts | Native desktop helper | PNG/blob + protobuf over pinned local ActuatorService to LS; onward cloud destination unknown | Enabled computer action/capture | CUA flag, per-app consent/TCC, pause/stop/quit | handler.js (computerActuator):1745-1819,1208-1217 |
| Accessibility text / element references, entered text | Native AX and computer requests | Local LS via actuator results / native helper pipes | AX snapshot/actions | Same gates; background-only batches avoid display capture | macComputerDriver.js:10-24; handler.js:1745-1752 |
| H264 window video, frame/agent/activity metadata | Helper preview | Local Hub via loopback-only WebRTC; paired remote peer or TURN only when remote mode enabled | Live preview/viewer session | Pane close/TTL/stop; packaged remote mode off | rtcSender.js:14-25; viewerHandler.js:41-55 |
| Device public-key hash, label, pairing timestamp | Remote viewer ECDSA proof | userData desktop_view_pairings.json, local logs | User-approved pairing | Device revoke closes live sessions | pairing.js:174-239; viewerHandler.js:260-296 |
| Stable desktop actuator identifier | Hash of hostname, then persisted | Local LS actuator registration | CUA registration | Disable CUA; no independent identity control shown | desktopHost/config.js:53-80 |
| Browser profile cookies/site data/history-like state | Agent/browser sites | Dedicated persistent browser profiles/guest partitions; target sites | User or agent browsing/login | Per-site clear data exposed; avoid authenticated agent browsing | browserEngine.js:80-123; preload.js:211-220 |
| Sanitized URLs/headers/status/body snippets | Guest CDP network | In-memory 50-entry ring, Hub renderer; onward LS use not established here | Guest browsing/network events | Clear requests API; no global capture opt-out found in this module | guestNetworkController.js:9-11,292-405 |
| QR/session secret, BLE ephemeral ads, RP/challenge, credential assertions | caBLE ceremony and phone authenticator | Local scanner/process; encrypted WSS to caBLE tunnel and returned guest assertion | Passkey request | Cancel/timeout/phone approval; scoped top-frame checks | cablev2.js:435-473,1695-1815,1923-2031 |
| PAC content and proxy destinations | Installed Chrome BeyondCorp extension files | Electron guest session proxy configuration / designated proxies or DIRECT | Browser setup | Extension/PAC absent => no imported PAC; no admin consent prompt shown | beyondcorpPac.js:139-240 |
| OS permission status/display geometry/versions | Electron/native helper probe | Local LS computer Hello | Enabled CUA registration/grant changes | CUA disabled; TCC denied | computer/index.js:115-145 |
| Electron logs / LS diagnostics and crash stacks | Console, stdout/stderr | App logs dir, local renderer log API | Running app, LS errors/auth output | No application-level retention control found | main.js:338-344; languageServer.js:282-344,383-431 |
| Font HTTP request metadata | IDE wizard HTML CSS import | fonts.googleapis.com; actual response subresources not established statically | Wizard rendering | Do not open wizard; no font toggle shown | ideInstall/wizardHtml.js:22 |
| MCP usage telemetry | MCP runtime, if enabled elsewhere | play.googleapis.com/log?format=json_proto | **Disabled for Hub-spawned MCP by shell env** | Forced off by Hub; standalone config can differ | languageServer.js:268-275; MCP/config/ServerConfig.js:147-152 |

## Endpoint and hostname inventory

This inventory separates **actual endpoint literals**, **computed routes**, and **policy-only/comment hostnames**. Presence is not evidence of a connection. It covers distributed `.js` under `dist`, including comments and mock literals; dependency-only endpoints are explicitly separate. Runtime user URLs, sidecar maps, computed caBLE domains and ICE/TURN hosts cannot be exhaustively known from static literals. Escaped auth/callback patterns are normalized below for readability.

### Endpoint literals / route templates in dist

| URL / route | Evidence / purpose |
|---|---|
| `https://127.0.0.1` / `https://127.0.0.1:<LS-port>/` | constants.js:7; computer/previewViewers.js:34, main UI |
| `https://${host}:${port}` | actuator/nodeTransport.js:200, pinned LS transport |
| `https://${host}/`, `https://${h}/*` | webviewSecurityState.js:259,265,495, certificate/site-data patterns |
| `http://localhost:<port>` / `http://127.0.0.1:<port>/` | customScheme.js:10; remote/transport.js:25; remote/remoteSidecars.js:187; callback URL templates |
| `http://${expectedHost}` | hostBridgeServer.js:193, loopback host bridge |
| `http://${REMOTE_LOOPBACK}:${ls.httpPort}/exa.language_server_pb.LanguageServerService/GetSidecars` | remote/remoteSidecars.js:104 |
| `http://${REMOTE_LOOPBACK}:${port}${url.pathname}${url.search}` | remote/remoteSidecars.js:258, sidecar proxy |
| `https://${REMOTE_LOOPBACK}:${discovery.httpsPort}/` | remote/sshConnection.js:1055 |
| `http://${REMOTE_LOOPBACK}:${httpPort}/healthz` | remote/sshConnection.js:1067 |
| `http://${REMOTE_LOOPBACK}:${d.httpPort}/healthz` | remote/sshConnection.js:651 |
| `http://${REMOTE_LOOPBACK}:${d.httpPort}/auth/mint` | remote/sshConnection.js:565 |
| `http://${REMOTE_LOOPBACK}:${d.httpPort}/exa.language_server_pb.LanguageServerService/Exit` | remote/sshConnection.js:458 |
| `http://${REMOTE_LOOPBACK}:${d.httpPort}/exa.language_server_pb.LanguageServerService/${method}` | remote/sshConnection.js:515 |
| `http://${REMOTE_LOOPBACK}:${port}${pathAndQuery}` | remote/sshConnection.js:1342 |
| `http://127.0.0.1:9` | scriptJail.js:90, blackhole proxy, not collector |
| `http://127.0.0.1` | wcvBrowserRegistry.js:223,778; wcvZOrderController.js:3176, URL parsing bases |
| `http://localhost` | remote/signInCallback.js:86; cablev2.js:957, parsing/example |
| `https://127.0.0.1:1234/` | __mocks__/electron.js:115,170, test-only |
| `https://generativelanguage.googleapis.com` | lsDefaults.js:6, LS API override |
| `https://daily-cloudcode-pa.googleapis.com` | lsDefaults.js:7, LS Cloud Code override |
| `https://accounts.google.com/o/oauth2/auth...` | languageServer.js:112 escaped regex, external auth URL recognition |
| `https://accounts.google.com` + `/o/oauth2/auth`, `/o/oauth2/v2/auth` | remote/signInUrls.js:29-38 |
| `https://auth.cloud.google/authorize` | remote/signInUrls.js:47-48 |
| `https://www.googleapis.com/auth/cloud-platform` | remote/signInUrls.js:19,52, OAuth scope URI |
| `https://www.googleapis.com/auth/userinfo.email` | remote/signInUrls.js:20, scope |
| `https://www.googleapis.com/auth/userinfo.profile` | remote/signInUrls.js:21, scope |
| `https://www.googleapis.com/auth/cclog` | remote/signInUrls.js:22, scope |
| `https://www.googleapis.com/auth/experimentsandconfigs` | remote/signInUrls.js:23, scope |
| `https://www.googleapis.com/auth/aicode` | remote/signInUrls.js:32, scope |
| `https://antigravity.google/docs` | menu.js:176, user-opened docs |
| `https://antigravity.google/auth-success` | remote/signInReturn.js:15 |
| `https://antigravity-hub-auto-updater-974169037036.us-central1.run.app` | wsl.js:66, server artifact manifest origin |
| `https://storage.googleapis.com/antigravity-public` | wsl.js:68, artifact base |
| `https://antigravity-ide-auto-updater-974169037036.us-central1.run.app/api/update/${platformKey}/stable/latest` | ideInstall/constants.js:60 |
| `https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap` | ideInstall/wizardHtml.js:22 |
| `wss://${domain}/cable/connect/${routingIdHex}/${tunnelIdHex}` | cablev2.js:572; assigned cable.ua5v.com / cable.auth.com plus derived domains |
| `https://agy-ssh.invalid/<action>?page=<token>` / `https://agy-ssh.invalid` | remote/disconnectedPage.js:43,50, intercepted local action sentinel, not real server |
| `https://source.chromium.org/chromium/chromium/src/+/main:chrome/browser/external_protocol/external_protocol_handler.cc` | externalUrl.js:14, source comment |
| `http://www.d-project.com/` | qrEncoder.js:9,13, attribution only |
| `http://www.opensource.org/licenses/mit-license.php` | qrEncoder.js:16, license attribution only |
| `http://www.denso-wave.com/qrcode/faqpatent-e.html` | qrEncoder.js:37, attribution only |
| `http://www.w3.org/2000/svg` | loadingOverlay.js:52; qrEncoder.js:890, XML namespace, not fetch |
| `antigravity://...`, configured variant `...://oauth-success` | main.js:230-241; remote/signInReturn.js:17, OS callback |
| `plugin://<authority>`, `agy-favicon://<token>/` | customScheme.js:10-18,37-68, local protocols |

`remote/bootstrapScript.js:893,900` contains shell `https://*` case patterns and redirect-protocol restrictions, not a host. Local RPC path `/exa.language_server_pb.LanguageServerService/GetMendelFlags` is in `mendelFlagSync.js:54`; the hostname/port is passed separately. Updater `/manifest/` URL comes from YAML outside dist, as documented in section 8.

### All additional hostname/suffix literals (policy, documentation or identity data)

The following are real hostname-shaped literals found in distributed JS, not additional proven destinations. Public-suffix tokens are intentionally included because the request asks for every hostname/URL; bare source-code property chains such as `sender.id`, `console.info`, `this.app`, and bundle filenames `Chrome.app` are not hostnames. The two OAuth `*.apps.googleusercontent.com` strings are client IDs, listed in section 9 rather than classified as network destinations.

**Identity, protected-network, corporate and sensitive Google host lists** (`guestNetworkSanitizer.js:22-104`, `guestWebMcpController.js` protected hosts, browser policy sources):

```
accounts.google.com accounts.youtube.com admin.google.com ads.google.com
analytics.google.com api.amazon.com api.dropboxapi.com api.twitter.com api.x.com
appleid.apple.com auth0.com bitbucket.org business.google.com calendar.google.com
chat.google.com colab.research.google.com community.auth0.com console.cloud.google.com
contacts.google.com corp.google.com developer.auth0.com developer.okta.com
discord.com docs.auth0.com docs.google.com docs.okta.com drive.google.com
duosecurity.com facebook.com gds.google.com gemini.google.com github.com gitlab.com
google.com googleapis.com graph.facebook.com groups.google.com help.auth0.com
help.okta.com identitytoolkit.googleapis.com idp.prp.tpcpe.goog keep.google.com
login.corp.google.com login.google.com login.live.com login.microsoftonline.com
login.yahoo.com lookerstudio.google.com mail.google.com mainsail.exceedlms.com
meet.google.com metadata.goog metadata.google.internal myaccount.google.com
oauth2.googleapis.com okta-emea.com okta.com oktapreview.com onelogin.com
passwords.google.com pay.google.com photos.google.com pingidentity.com
script.google.com securetoken.googleapis.com shell.cloud.google.com sites.google.com
slack.com ssh.cloud.google.com sso.google.com status.auth0.com status.okta.com
sts.googleapis.com support.auth0.com support.okta.com takeout.google.com
uberproxy-pen-redirect.corp.google.com uberproxy.corp.google.com voice.google.com
websso.corp.google.com www.facebook.com www.github.com www.googleapis.com
```

**Payments/consequential-action host policy literals** (`guestWebMcpController.js`):

```
adyen.com affirm.com afterpay.com braintree-api.com braintreegateway.com
checkout.shopify.com js.checkout.com klarna.com pay.amazon.com pay.checkout.com
payments.amazon.com payments.google.com paypal.com paypalobjects.com shop.app
shopifycs.com square.site squareup.com squareupsandbox.com stripe.com wallet.google.com
```

**Shared-hosting/site and RP public-suffix tokens** (`guestCrossSiteCredentials.js:24-174`; `cablev2.js:900-940` and preceding suffix entries):

```
ac.in ac.jp ac.kr ac.uk ac.za amazonaws.com appspot.com azureedge.net
azurestaticapps.net azurewebsites.net bitbucket.io blob.core.windows.net blogspot.com
cloud.goog cloudapp.net cloudflareworkers.com cloudfront.net cloudfunctions.net
co.id co.il co.in co.jp co.ke co.kr co.nz co.th co.uk co.ve co.za codesandbox.io
com.ar com.au com.br com.cn com.co com.eg com.hk com.mx com.my com.ng com.pe
com.ph com.pk com.sa com.sg com.tr com.tw com.ua com.vn corp.goog csb.app deno.dev
edu.au edu.cn firebaseapp.com firebaseio.com fly.dev framer.app github.io
githubusercontent.com gitlab.io gitpod.io glitch.me go.jp go.kr gob.mx
googleplex.com googlers.com googleusercontent.com gov.au gov.cn gov.in gov.uk gov.za
herokuapp.com hf.space ltd.uk me.uk myshopify.com ne.jp net.au net.br net.cn net.in
net.nz net.uk netlify.app ngrok-free.app ngrok.io notion.site ondigitalocean.app
onrender.com or.jp or.kr or.th org.au org.br org.cn org.in org.nz org.uk org.za
pages.dev plc.uk readthedocs.io repl.co replit.dev run.app s3.amazonaws.com
sharepoint.com stackblitz.io streamlit.app substack.com surge.sh translate.goog
trycloudflare.com vercel.app web.app webcontainer.io webflow.io wixsite.com
wordpress.com workers.dev
```

**Other comment/example/protocol literals:** `alice.github.io`, `bob.github.io`, `example.co.uk`, `example.com.ng`, `example.or.th` (`guestCrossSiteCredentials.js` examples); `golang.org` (`remote/signInUrls.js:66` comment); `openssh.com` (SSH documentation/policy text); `cable.ua5v.com`, `cable.auth.com` (actual assigned tunnel domains); localhost, 127.0.0.1, ::1 and IPv4-mapped loopback; `.local` (remote ICE mDNS filtering). `surge.sh` is a policy suffix despite not matching the narrower common-TLD extraction used for an initial search.

**Outside-dist dependency/config endpoints:** Hub update URL `/manifest/` above; MCP dormant `https://play.googleapis.com/log?format=json_proto`; MCP update checker `https://registry.npmjs.org` (also forcibly disabled for Hub-spawned MCP). These must not be conflated with shell analytics enabled by default.

## Overall privacy assessment and limits

The shell clearly handles sensitive local material: exported environment secrets, authenticated browser sessions, page/network snippets, computer screenshots and accessibility text, auth callbacks, persistent settings, diagnostics and pseudonymous identifiers. The most consequential privacy boundary is local helper/browser → privileged Hub/LS → downstream model/service; only the first hop and configured LS service roots are established here. User choices enabling computer use or an agent browser session can expose far more than source code, including other apps' UI and account data; per-app permission/redaction and actual LS retention/transmission policy need to be evaluated together.

Good controls include dedicated browser profiles/pipe CDP, explicit WCV isolation, separate script jail networking, empty-env native script child plus selftest gate, helper env allowlist, separate authenticated loopback update bridge, pinned actuator TLS, browser network redaction, signed paired remote-view proofs and default-off packaged remote viewing. They do not cancel the downgrade setting, broad guest permission grants, unpinned UI/flags loopback trust, log exposure or broad LS environment inheritance.

This is source/metadata evidence, not measured runtime traffic or a certification of native helper enforcement, server-side consent, data retention, model training use, cloud access control, native Squirrel signature validation, or downstream OAuth token storage. No such unobserved guarantee is implied.

### Local-only URI and address supplement

The endpoint inventory also includes non-network settings/document URIs: `x-apple.systempreferences:com.apple.preference.security?Privacy_Accessibility` and `...?Privacy_ScreenCapture` (`computer/permissions.js:14-15`), `x-apple.systempreferences:com.apple.preference.notifications` and `ms-settings:notifications` (`ipcHandlers.js:316,319`), `x-apple.systempreferences:com.apple.preference.security?Privacy_Microphone` and `ms-settings:privacy-microphone` (`ipcHandlers.js:424,427`). These invoke OS settings, not remote hosts. `about:blank`, `about:srcdoc`, `data:text/html,...`, `data:image/...;base64,...`, `data:application/x-ns-proxy-autoconfig;base64,...`, blob/file URLs and the `FIDO:/<digits>` QR URI are local/generated document or authentication payload schemes, not collection endpoints. Denied protocol names are enumerated in externalUrl.js rather than actual launched endpoint literals.

Additional address literal `100.100.100.200` (`webviewBrowser.js:388`) is a protected cloud-metadata address, not a configured remote service. Loopback/metadata classification also contains numerical and IPv6 matching patterns rather than a finite list of destination hostnames.
