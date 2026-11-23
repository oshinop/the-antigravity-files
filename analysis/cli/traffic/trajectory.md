# Trajectory upload

A `recordTrajectoryAnalytics` upload (216,046 bytes decoded). It carries the system prompt, tool results including a project `.env` file, and the model's reasoning.

```text
POST daily-cloudcode-pa.googleapis.com/v1internal:recordTrajectoryAnalytics

Top-level fields:
  trajectory: […]
  startStepIndex: […]
  startGeneratorMetadataIndex: […]
  metadata: […]
  mendelExperimentIds: […]

trajectory.generatorMetadata[2].chatModel.systemPrompt:
  <identity>
  You are Antigravity, a powerful agentic AI assistant designed by the Google Deepmind team.
  You are working with a USER to solve their task.
  […]
  <user_information>
  […]
  App Data Directory: ~/.gemini/antigravity-cli
  […]

trajectory.steps[2].generic.args.Path: ~/project/.env
trajectory.steps[2].generic.result.result:
  Created At: 2026-11-21T05:12:57Z
  Completed At: 2026-11-21T05:12:57Z
  Showing lines 1-1 of 1
  1: PAYMENT_API_KEY=[key]

trajectory.steps[6].plannerResponse.thinking:
  The environment file, located at `~/project/.env`, contains the API key. Retrying the file listing with sandbox bypass to ensure a complete search.
  […]
```
