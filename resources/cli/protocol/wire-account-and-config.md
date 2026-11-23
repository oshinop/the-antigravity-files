# Account, quota, model and configuration calls

The requests `agy` makes at start and in the background, with their response layouts. All Cloud Code calls are `POST https://daily-cloudcode-pa.googleapis.com/v1internal:<method>` with `Content-Type: application/json`, the OAuth bearer token and the `antigravity/cli/1.5.14 (…)` user agent, unless noted. Strings are replaced by their length or a placeholder. What the responses decide is in [sign-in-and-plans.md](../../../analysis/cli/sign-in-and-plans.md) and [server-switches.md](../../../analysis/cli/server-switches.md); the order of calls is in [Protocols and request flows](protocol-and-requests.md#start-of-a-session).

## Sign-in

```text
GET accounts.google.com/o/oauth2/auth        (opened in your browser)
  ?access_type=offline
  &client_id=[installed-app client ID]
  &code_challenge=<43 chars>&code_challenge_method=S256
  &prompt=consent
  &redirect_uri=https://antigravity.google/oauth-callback
  &response_type=code
  &scope=https://www.googleapis.com/auth/cloud-platform https://www.googleapis.com/auth/userinfo.email
         https://www.googleapis.com/auth/userinfo.profile https://www.googleapis.com/auth/cclog
         https://www.googleapis.com/auth/experimentsandconfigs https://www.googleapis.com/auth/aicode openid
  &state=<22 chars>

POST oauth2.googleapis.com/token        (form; User-Agent: Go-http-client/1.1)
  code, code_verifier, redirect_uri, client_id, client_secret, grant_type
→ access_token, expires_in, id_token, refresh_token, scope, token_type

POST oauth2.googleapis.com/token        (refresh)
  refresh_token, client_id, client_secret, grant_type
→ a new access token, or 400 {error, error_description} when the refresh token is no longer valid

GET www.googleapis.com/oauth2/v2/userinfo
→ { id, email, verified_email, name, given_name, family_name, picture }
```

## loadCodeAssist

```text
→ {"metadata": {"ideType": "ANTIGRAVITY"}}                                      (38 bytes)

← {
    "currentTier":   { "id", "name", "description",
                       "privacyNotice": { "showNotice": true, "noticeText": "<1,113 chars>" },
                       "upgradeSubscriptionUri", "upgradeSubscriptionText", "upgradeSubscriptionType" },
    "allowedTiers":  [ { "id", "name", "description", "privacyNotice": {…}, "isDefault": true,
                         "userDefinedCloudaicompanionProject", "usesGcpTos" }, … 2 tiers ],
    "cloudaicompanionProject": "[project ID]",
    "gcpManaged": false,
    "upgradeSubscriptionUri": "…",
    "paidTier":      { "id", "name", "description", "upgradeSubscriptionUri", "upgradeSubscriptionText",
                       "availableCredits": [ { "creditType", "minimumCreditAmountForUsage" } ] }
  }                                                                             (3,926 bytes)
```

`cloudaicompanionProject` becomes the `project` field of every later call.

## Calls that send only the project

`fetchAdminControls`, `fetchUserInfo`, `retrieveUserQuotaSummary`, `fetchAvailableModels` and `listExperiments` all send `{"project": "[project ID]"}` (30 bytes).

```text
fetchAdminControls        ← {}                                                  (for a consumer account)

fetchUserInfo             ← { "userSettings": { "telemetryEnabled": true }, "regionCode": "[region]" }

retrieveUserQuotaSummary  ← { "description": "<379 chars>",
                              "groups": [ { "displayName", "description",
                                            "buckets": [ { "bucketId", "displayName", "description", "window",
                                                           "resetTime", "remainingFraction" }, … ] }, … 3 groups ] }
```

### fetchAvailableModels (342 KB)

```text
← {
    "models": {                                           39 entries keyed by model ID, e.g.
      "gemini-4-argon-low": {
        "displayName", "model", "apiProvider", "modelProvider", "recommended", "tagTitle", "tagDescription",
        "maxTokens", "maxOutputTokens", "thinkingLevel", "minThinkingBudget",
        "supportsImages", "supportsVideo", "supportsThinking",
        "supportedMimeTypes": { "<56 MIME types>": true },
        "quotaInfo": { "remainingFraction", "resetTime" },
        "modelExperiments": { "experiments": {
            "CASCADE_USE_EXPERIMENT_CHECKPOINTER": { "stringValue": "<4,812 chars>" },
            "allow-experimental-monitors":        { "boolValue": true },
            "cascade-include-ephemeral-message":  { "stringValue": "<202 chars>" },
            "retry_model_capacity_exhausted":     { "boolValue": true },
            "task-details-suffix":                { "stringValue": "<400 chars>" } } },
        "modelExperimentIds": [ … ]
      },
      …
    },
    "defaultAgentModelId": "gemini-4-neon-high",
    "agentModelSorts": [ { "displayName", "groups": [ { "modelIds": [ … ] } ] } ],
    "groupedModels": { "<6 model families>": { "displayName", "supportedThinkingLevels": [ { "thinkingDisplayName", "thinkingSuffix" } ] } },
    "tieredModelIds": { "flashLite", "flash", "pro" },
    "commandModelIds", "imageGenerationModelIds", "mqueryModelIds", "webSearchModelIds",
    "commitMessageModelIds", "audioTranscriptionModelIds": [ … ],
    "sharedModelExperiments": { "sonar": … },
    "experimentIds": [ <76 numbers> ]
  }
```

Some entries carry extra capability keys (`supportsAdaptiveThinking`, `vertexModelId`, `toolFormatterType`, `supportsCumulativeContext` and others). `sharedModelExperiments.sonar` holds the monitor configuration shared by all models ([safety-reviews.md](../../../analysis/cli/safety-reviews.md)).

### listExperiments (68 KB)

```text
← { "experimentIds": [ <96 numbers> ],
    "flags": [ { "name": "<switch name>", "boolValue" | "intValue" | "floatValue" | "stringValue"
                 | "stringListValue" | "int32ListValue": …, "flagId"?: … }, … 635 switches ] }
```

Of the 635 switches, 490 are booleans, 67 strings, 61 integers, 8 floats, 7 string lists and 2 integer lists; 33 also carry a numeric `flagId`.

## fetchConfig

```text
GET https://aicode.googleapis.com/v1alpha:fetchConfig                           (no body; every 60 s)

← {
    "experimentConfig": { "enabledExperimentIds": [ <29 numbers> ],
                          "experimentFlags": [ { "name", "boolValue" }, … 436 flags ] },
    "availableExperiences": [ { "name",
                                "model": { "modelId", "name", "displayName", "baseModelId", "version", "description",
                                           "inputTokenLimit", "outputTokenLimit", "supportedThinkingLevels",
                                           "supportedFeatures", "generationConfig", "modelProvider",
                                           "agySupportedMimeTypes" },
                                "surfaceConfig": { "clientModelEnumName", "tags", "modelExperiments" } }, … 19 ],
    "agySurfaceConfig": { "agentExperiences", "imageGenerationExperiences", "commitMessageExperiences",
                          "webSearchExperiences", "tieredFlashLiteExperiences", "tieredFlashExperiences",
                          "tieredProExperiences", "audioTranscriptionExperiences": { "experienceNames": [ … ] } }
  }                                                                             (132,384 bytes)
```

## cascadeNuxes and plugin catalogues

```text
GET daily-cloudcode-pa.googleapis.com/v1internal/cascadeNuxes?page_size=1000
← { "cascadeNuxes": [ { "uid", "location", "priority", "title", "body", "imageUrl", "videoUrl",
                        "filter": { "clients", "subclients", "versionFilter": { "minVersion" }, "expirationDate" },
                        "session" }, … 3 ] }

GET www.gstatic.com/antigravity/plugins/marketplace.json
GET www.gstatic.com/antigravity/plugins-partners/marketplace.json
    (no Authorization; Cache-Control and If-Modified-Since; usually 304)
```
