# Protocol

How `agy` talks to Google and to apps that drive it, and the field layout of each request and response.

| Document | What it is |
|---|---|
| [Protocols and request flows](protocol-and-requests.md) | How `agy` 1.5.14 talks to Google and to apps that drive it: which transport each request uses, in what order requests go out, and how each request and response is laid out. |
| [Account, quota, model and configuration calls](wire-account-and-config.md) | The requests `agy` makes at start and in the background, with their response layouts. |
| [ACP handshake](wire-acp.md) | What `agy acp` answers on stdout to an `initialize` request, and to `session/new` before sign-in. |
| [Model requests with a Gemini API key](wire-api-key.md) | With `GEMINI_API_KEY` set, `agy` 1.5.14 sends model calls to the Gemini Developer API through the Google Gen AI Go SDK instead of Cloud Code. |
| [Clearcut envelope](wire-clearcut.md) | The protobuf layout of a `play.googleapis.com/log` batch, decoded by field number and named from `wireless_android_play_playlog.LogRequest` and `devtools.jetski.telemetry.JetskiTelemetryExtension`. |
| [Model request and response stream](wire-model-request.md) | One main-agent call from the second prompt of a session, after 17 steps, and the stream that answered it. |
| [Review, adjudicator, title and compaction requests](wire-side-model-requests.md) | The model calls `agy` makes besides the agent's own. |
| [Trajectory registration and upload](wire-trajectory.md) | The two Cloud Code calls that register and upload a conversation's trajectory. |
| [Web search request](wire-web-search.md) | The request `agy` 1.5.14 sends when the agent calls its web search tool. |
