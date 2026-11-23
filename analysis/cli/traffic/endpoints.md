# Endpoints contacted in a session

Every HTTP destination contacted in one interactive session, grouped by host and path. Sent bytes are decoded request bodies only.

```text
Host / path                                                        Requests      Sent (B)  Purpose
play.googleapis.com/log                                                  25     3,918,684  Clearcut telemetry
aicode.googleapis.com/v1alpha:fetchConfig                                  5             0  Configuration download
www.googleapis.com/oauth2/v2/userinfo                                     2             0  Account profile
lh3.googleusercontent.com/a/[avatar ID]                                   2             0  Account avatar
www.gstatic.com/antigravity/plugins/marketplace.json                      1             0  Plugin marketplace
www.gstatic.com/antigravity/plugins-partners/marketplace.json             1             0  Partner plugin marketplace
daily-cloudcode-pa.googleapis.com:
  /v1internal:streamGenerateContent                                      33     1,229,979  Model inference and review
  /v1internal:recordTrajectoryAnalytics                                   4     1,006,669  Trajectory uploads
  /v1internal:writeTrajectoryAcls                                         2           110  Trajectory ID registration
  /v1internal:loadCodeAssist                                              4           152  Service/account bootstrap
  /v1internal:fetchUserInfo                                                2            60  Account settings
  /v1internal:fetchAdminControls                                           1            30  Admin controls
  /v1internal:listExperiments                                             1            30  Experiment flags
  /v1internal:fetchAvailableModels                                        1            30  Models and their configuration
  /v1internal:retrieveUserQuotaSummary                                    5           150  Quota summary
  /v1internal/cascadeNuxes                                                1             0  Onboarding configuration
Total                                                                    90     6,155,894
```
