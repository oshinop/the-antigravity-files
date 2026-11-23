# Clearcut envelope

The protobuf layout of a `play.googleapis.com/log` batch, decoded by field number and named from `wireless_android_play_playlog.LogRequest` and `devtools.jetski.telemetry.JetskiTelemetryExtension`. Values that identify the machine or account are replaced by placeholders. What the records carry, including file contents and prompts, is in [data-collection.md](../../../analysis/cli/data-collection.md) and the excerpts [clearcut-tool-step.md](../traffic/clearcut-tool-step.md) and [clearcut-optout.md](../traffic/clearcut-optout.md).

```text
POST https://play.googleapis.com/log
Content-Type: application/octet-stream
Authorization: Bearer [OAuth access token]          (absent before sign-in)
User-Agent: Go-http-client/2.0
(142 bytes to 583 KB per batch)

LogRequest
  1  client_info { 1 client_type: 16 }
  2  log_source: 2747
  3  log_event {                                     repeated; up to hundreds per batch
       1  event_time_ms: [epoch ms]
       6  source_extension: JetskiTelemetryExtension {
            16 ide_name:     "antigravity"
            17 ide_version:  "1.5.14"
            18 os:           "[os]"
            19 arch:         "[arch]"
            22 user_tier_id: "[tier]"                 empty before sign-in
            24 is_redacted:  1                        on some records
            25 region_code:  "[region]"
            27 product_name: "cli"
             6 analytics_event_metadata {             one payload per record; here an analytics event
                 1 event_type: "hub_store_load_succeeded"
                 2 extra { 1 "file_type",       2 "app_state" }
                 2 extra { 1 "ideVersion",      2 "" }
                 2 extra { 1 "machineId",       2 "" }
                 2 extra { 1 "antigravity_ide_installation_id", 2 "" }
                 2 extra { 1 "installation_id", 2 "[installation ID]" }
                 […]
               }
          }
       11 event_code: <n>
     }
  4  request_time_ms: [epoch ms]

LogResponse
  1  next_request_wait_millis: 5000
  3  […]
```

The payload field of `JetskiTelemetryExtension` varies by record. Across four sessions, 3,966 records on log source 2747 carried:

| Field | Payload | Records |
|---|---|---|
| 38 | `hook_execution` | 2,196 |
| 30 | `monitor_signal` | 801 |
| 12 | `cortex_trajectory_step` | 436 |
| 6 | `analytics_event_metadata` | 341 |
| 3 | `cortex_generator_metadata` | 128 |
| 2 | `cortex_execution_metadata` | 19 |
| 11 | `cortex_trajectory` | 16 |
| 13 | `state_initialization_data` | 15 |
| 15 | `error_trace_metadata` | 14 |

Two other log sources share the endpoint:

```text
LogRequest                                           log source 327: error traces
  1  client_info { 1 client_type: 16
                   3 desktop_client_info { 3 os: "[os]", 6 application_build: "1.5.14",
                                           10 application_identifier: "antigravity_desktop" } }
  2  log_source: 327
  3  log_event {
       1  event_time_ms
       6  source_extension {
            2 { 2 { 4 "antigravity_desktop", 5 "1.5.14" } }
            3 { 1 { [timestamp] }, 2 "main", 3 1000, 4 "LS_ERROR", 5 "antigravity-cli",
                6 "<error message>", 8 { <the error with its stack> } }
            18 { 2 { 1 3, 3 "[32 hex]", 5 "[installation ID]", 6 2 } }
          }
       11 event_code
     }

LogRequest                                           log source 2992: the Clearcut library's own upload report
  1  client_info { 1 client_type: 16 }
  2  log_source: 2992
  3  log_event {
       1  event_time_ms
       6  source_extension: ClearcutGolangClientTelemetry {
            10 telemetry_windows {
                 10 logger_id, 11 sequence_number, 12 log_source: 2747,
                 13 window_start_time_ms, 14 window_end_time_ms, 15 num_records_logged,
                 16 uploads { 10 upload_start_time_ms, 11 events_attempted, 12 http_status: 200,
                              13 events_uploaded, 18 events_removed_from_buffer, 19 upload_end_time_ms,
                              20 upload_payload_size_bytes, 21 http_request_content_bytes, 22 upload_result }   repeated
               }
          }
     }
```

The source-327 record is shown by field number; its message type isn't named in the request.
