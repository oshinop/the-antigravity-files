# Endpoints contacted in a 1.5.14 session

Every HTTP destination contacted in four minutes of an interactive `agy` 1.5.14 session (two prompts, one of them delegated to a research helper), with "Enable Telemetry" on. Sent bytes are decoded request bodies only.

```text
Host / path                                                        Requests      Sent (B)  Purpose
play.googleapis.com/log                                                  15     2,124,513  Clearcut telemetry
oauth2.googleapis.com/token                                               1           304  Token refresh
aicode.googleapis.com/v1alpha:fetchConfig                                  6             0  Configuration download
www.googleapis.com/oauth2/v2/userinfo                                     2             0  Account profile
lh3.googleusercontent.com/a/[avatar ID]                                   2             0  Account avatar
www.gstatic.com/antigravity/plugins/marketplace.json                      1             0  Plugin marketplace
www.gstatic.com/antigravity/plugins-partners/marketplace.json             1             0  Partner plugin marketplace
daily-cloudcode-pa.googleapis.com:
  /v1internal:streamGenerateContent                                      24       629,493  Model inference and review
  /v1internal:recordTrajectoryAnalytics                                   3       618,323  Trajectory uploads
  /v1internal:writeTrajectoryAcls                                         2           110  Trajectory ID registration
  /v1internal:loadCodeAssist                                              4           152  Service/account bootstrap
  /v1internal:retrieveUserQuotaSummary                                    5           150  Quota summary
  /v1internal:fetchUserInfo                                                2            60  Account settings
  /v1internal:fetchAdminControls                                           1            30  Admin controls
  /v1internal:fetchAvailableModels                                        1            30  Models and their configuration
  /v1internal:listExperiments                                             1            30  Experiment flags
  /v1internal/cascadeNuxes                                                1             0  Onboarding configuration
Total                                                                    72     3,373,195
```
