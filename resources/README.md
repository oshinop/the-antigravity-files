# Resources

The reference library: the material as Google ships or sends it, records of what `agy` sent and received, and how its parts talk to each other. Each folder has an `INDEX.md` catalogue.

| Catalogue | Contents |
|---|---|
| [System prompts](cli/system-prompts/INDEX.md) | 6 system prompts sent in `agy` 1.5.14 requests: main agent, swarm member, research helper, command adjudicator, title generator |
| [Tool declarations](cli/tool-declarations/INDEX.md) | The tools given to 4 callers, with each tool's description |
| [Server switches](cli/server-switches/INDEX.md) | All 635 switches Google's server sends at start, by what `agy` does with them, plus 8 per-model switches |
| [Binary 1.5.14](cli/binary-1.5.14/INDEX.md) | 259 prompt assets embedded in the current `agy` binary |
| [Binary 1.5.11](cli/binary-1.5.11/INDEX.md) | 262 prompt assets from the previous binary, with where each sits in it |
| [Protocol](cli/protocol/INDEX.md) | Transports, request order, ACP, and the field layout of each request and response |
| [Traffic excerpts](cli/traffic/INDEX.md) | Excerpts of captured requests and responses: telemetry, uploads, reviews, injected messages, helpers, compaction |
| [One session, traced](cli/session-1/README.md) | Timeline of a two-prompt session: the helper outliving the turn, review models checking tool calls, telemetry throughout |
| [CLI protos](cli/protos/INDEX.md) | 351 protobuf definitions from the `agy` 1.5.14 binary, each compared with the desktop backend's copy |
| [Desktop protos](desktop/protos/INDEX.md) | 550 protobuf definitions from the Antigravity 2.33.0 desktop backend, each compared with `agy`'s copy |
| [Desktop protocol](desktop/protocol/INDEX.md) | Local RPC between the desktop app and its language server, and the message layouts |
| [Diagrams](diagrams/INDEX.md) | How swarm, Teamwork, helper delegation, the review chain, telemetry and compaction work |
| [Sources](sources/INDEX.md) | Google's Terms, FAQ and forum reply, and every public page the article cites |
