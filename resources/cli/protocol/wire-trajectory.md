# Trajectory registration and upload

The two Cloud Code calls that register and upload a conversation's trajectory. Strings are replaced by their length or a placeholder. What the steps and generator records contain, and what switching telemetry off changes, is in [data-collection.md](../../../analysis/cli/data-collection.md); an upload with its content is in [trajectory.md](../traffic/trajectory.md).

## writeTrajectoryAcls

Sent when a trajectory starts: once for the main conversation, and once for each helper it launches.

```text
POST daily-cloudcode-pa.googleapis.com/v1internal:writeTrajectoryAcls
→ {"trajectoryId": "[uuid]"}                                                    (55 bytes)
← {}
```

## recordTrajectoryAnalytics

Sent when a turn ends, the main agent's or a helper's, as JSON. Seven uploads in one session were 159 KB to 406 KB each.

```text
POST daily-cloudcode-pa.googleapis.com/v1internal:recordTrajectoryAnalytics
→ {
    "trajectory": {
      "cascadeId":      "[uuid]",
      "trajectoryId":   "[uuid]",
      "trajectoryType": "<30 chars>",
      "source":         "<28 chars>",
      "executorMetadatas": [ {
        "agentConfigResolution": { "requestedAgentSpec": { "builtinAgent": { "defaultAgent":
                                     { "isGoogle": false, "isInteractive": true, "surface": "<17 chars>" } } },
                                   "contribVariantMetadata": {} },
        "cascadeConfig": {
          "agentApiConfig", "battleModeConfig", "checkpointConfig" (compaction settings and summary prompt),
          "conversationHistoryConfig", "dynamicWorkspaceUris",
          "executorConfig": { "commandAssessorConfig": { "policyGuardianConfig": {…}, "sonarMonitorsConfigTextproto": "<31,467 chars>", … },
                              "maxGeneratorInvocations", "queueAllSteps", "requireFinishTool", … },
          "messageConfig", "modelTiers": { "flash", "flashLite", "pro" }, "plannerConfig": {…}, …
        } } ],
      "steps": [ { "type", "status", <one of: "userInput", "plannerResponse", "generic", "systemMessage">,
                   "error"?, "permissions"?, "completedInteractions"? }, … ],
      "generatorMetadata": [ { "chatModel": { <the full model request, system prompt included> },
                               "executionId", "stepIndices", "mendelExperimentIds", "plannerConfig"? }, … ],
      "metadata": { … }
    },
    "metadata": { "ideName", "ideVersion", "os", "hardware", "productName", "regionCode", "userTierId" },
    "mendelExperimentIds": [ … ],
    "startStepIndex": <n>,                   only when continuing an earlier upload of the same trajectory
    "startGeneratorMetadataIndex": <n>       likewise
  }
← {}
```

The first upload of a trajectory starts at step 0 and omits both index fields. A later upload of the same trajectory sends only the new steps and generator records, with `startStepIndex` and `startGeneratorMetadataIndex` giving where they start.
