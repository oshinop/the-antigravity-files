# Diagrams

| Figure | What it shows |
|---|---|
| [swarm.svg](swarm.svg) · [png](swarm.png) | How /swarm works: the coordinator briefs members by name on a shared board, members post results and claim unassigned work, and the overview shows each piece as working, waiting or done |
| [teamwork.svg](teamwork.svg) · [png](teamwork.png) | How /teamwork works: a sentinel routes the task to a pipeline; the orchestrator hands off to a fresh successor; a victory auditor with no shared context checks for faked results; proofs run as tournaments with falsifiers |
| [waiting.svg](waiting.svg) · [png](waiting.png) | Sequence: the main agent delegates and ends its turn while the helper is still running, and can kill the helper when the next prompt arrives |
| [review-pipeline.svg](review-pipeline.svg) · [png](review-pipeline.png) | Two separate reviews: the DeepMind monitor (local allow-list, step review, history review on Google's models) runs on Google's server switch; auto mode adds a reversibility check for commands leaving the sandbox |
| [data-flow.svg](data-flow.svg) · [png](data-flow.png) | Where a session's content goes with default settings: model inference, Clearcut telemetry, trajectory uploads and review models |
| [first-request.svg](first-request.svg) · [png](first-request.png) | Treemap of the 39 KB first request by section and tool, colored by purpose |
| [flags.svg](flags.svg) · [png](flags.png) | The 635 server switches grouped by what they control |
| [compaction.svg](compaction.svg) · [png](compaction.png) | Compaction limit against the window /context shows: Gemini 4 Argon compacts at 60K (your setting), Gemini 4 Neon and Hydrogen at 512K (Google's server), while /context reports a 1.0M window for all three |
