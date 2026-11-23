# Protobuf definitions: `agy` 1.5.14

Decoded from the 351 file descriptors embedded in the `agy` 1.5.14 binary. Each file renders the messages, enums, services and extensions of one descriptor; custom options are left out. Services list the RPCs the CLI can call or serve.

The last column compares each file with the same file in the [Antigravity 2.33.0 desktop backend](../../desktop/protos/INDEX.md). Of the 351 files, 253 are byte-identical, 21 differ only in imports or options, 48 differ in their definitions, and 29 are not in the desktop backend. The desktop backend has 228 files that this one doesn't.

Where a file differs, one side almost always has messages, fields, enum values or RPCs the other lacks. In four places the same field number carries a different field:

| File | Field | `agy` 1.5.14 | Desktop 2.33.0 |
|---|---|---|---|
| `gdm/security/agi_control/agent_monitoring/monitors/monitor_config.proto` | `GuardianAgentMonitorConfig` 5 | `float severity_threshold` | `bool trigger_on_confirm` |
| `learning/genai/api/interactions/proto/steps.proto` | `ManageTaskCallStep` 2 | `string task_id` | `string call_id` |
| `learning/genai/api/interactions/proto/steps.proto` | `ManageTaskResultStep.Task` 1 | `string task_id` | `string call_id` |
| `third_party/jetski/cortex_pb/cortex.proto` | `SidecarSandboxConfig` 5 | `repeated string allowed_http_hosts` | `repeated string allowed_hosts` |

| File | Package | Messages | RPCs | Desktop 2.33.0 |
|---|---|---|---|---|
| [apiserving__proto__media.proto](apiserving__proto__media.proto) | `apiserving` | 2 | 0 | identical |
| [apps__framework__data__service_annotations.proto](apps__framework__data__service_annotations.proto) | `af.data` | 0 | 0 | same schema, different options |
| [assistant__api__annotations__version_annotations.proto](assistant__api__annotations__version_annotations.proto) | `assistant.api.annotations` | 5 | 0 | identical |
| [assistant__api__core_types__device_type.proto](assistant__api__core_types__device_type.proto) | `assistant.api.core_types` | 4 | 0 | identical |
| [assistant__api__core_types__surface.proto](assistant__api__core_types__surface.proto) | `assistant.api.core_types` | 1 | 0 | identical |
| [blobstore2__public__request_class.proto](blobstore2__public__request_class.proto) | `blobstore2` | 0 | 0 | identical |
| [cloud__ai__platform__dataplane__cardolan__proto__compliance_annotation.proto](cloud__ai__platform__dataplane__cardolan__proto__compliance_annotation.proto) | `cloud_aiplatform.dataplane.cardolan` | 0 | 0 | identical |
| [cloud__developer_experience__cloudcode__pkg__modelproxy__protos__extra_fields.proto](cloud__developer_experience__cloudcode__pkg__modelproxy__protos__extra_fields.proto) | `cloud.developer_experience.cloudcode.pkg.modelproxy` | 1 | 0 | identical |
| [cloud__security__kms__boq__keytracking__proto__key_tracking_annotations.proto](cloud__security__kms__boq__keytracking__proto__key_tracking_annotations.proto) | `keytracking` | 0 | 0 | identical |
| [devrel__primes__protos__anonymization.proto](devrel__primes__protos__anonymization.proto) | `devrel.primes.protos` | 5 | 0 | identical |
| [devrel__primes__protos__primes_annotations.proto](devrel__primes__protos__primes_annotations.proto) | `devrel.primes.protos` | 1 | 0 | identical |
| [devtools__ai__flow__autocomplete__logging.proto](devtools__ai__flow__autocomplete__logging.proto) | `devtools.ai.autocomplete` | 2 | 0 | differs: 23 definitions only here, 0 only in the desktop backend |
| [devtools__cider__lsp__common.proto](devtools__cider__lsp__common.proto) | `editorservices` | 40 | 0 | identical |
| [devtools__cider__lsp__extension.proto](devtools__cider__lsp__extension.proto) | `editorservices` | 6 | 0 | identical |
| [devtools__cider__proto__annotations.proto](devtools__cider__proto__annotations.proto) | `cider.annotations` | 0 | 0 | identical |
| [devtools__cider__shared__proto__stats.proto](devtools__cider__shared__proto__stats.proto) | `editorservices.logging` | 1 | 0 | identical |
| [devtools__cog__proto__cog_workspace_state.proto](devtools__cog__proto__cog_workspace_state.proto) | `devtools_cog` | 3 | 0 | identical |
| [devtools__jetski__boq__api__proto__api.proto](devtools__jetski__boq__api__proto__api.proto) | `devtools_jetski_boq_api_proto` | 39 | 19 | differs: 5 definitions only here, 0 only in the desktop backend |
| [devtools__jetski__citc_attribution__proto__citc_attribution.proto](devtools__jetski__citc_attribution__proto__citc_attribution.proto) | `google.devtools.jetski.citc_attribution` | 2 | 0 | not in the desktop backend |
| [devtools__jetski__telemetry__extensions__analytics_event_metadata.proto](devtools__jetski__telemetry__extensions__analytics_event_metadata.proto) | `devtools.jetski.telemetry.extensions` | 2 | 0 | identical |
| [devtools__jetski__telemetry__extensions__battle_mode_metadata.proto](devtools__jetski__telemetry__extensions__battle_mode_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__command_usage_metadata.proto](devtools__jetski__telemetry__extensions__command_usage_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__common.proto](devtools__jetski__telemetry__extensions__common.proto) | `devtools.jetski.telemetry.extensions` | 2 | 0 | identical |
| [devtools__jetski__telemetry__extensions__completions_metadata.proto](devtools__jetski__telemetry__extensions__completions_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__cortex_execution_metadata.proto](devtools__jetski__telemetry__extensions__cortex_execution_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__cortex_generator_metadata.proto](devtools__jetski__telemetry__extensions__cortex_generator_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__cortex_trajectory.proto](devtools__jetski__telemetry__extensions__cortex_trajectory.proto) | `devtools.jetski.telemetry.extensions` | 2 | 0 | identical |
| [devtools__jetski__telemetry__extensions__cortex_trajectory_step.proto](devtools__jetski__telemetry__extensions__cortex_trajectory_step.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__error_trace_metadata.proto](devtools__jetski__telemetry__extensions__error_trace_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__feedback_metadata.proto](devtools__jetski__telemetry__extensions__feedback_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__input_completion.proto](devtools__jetski__telemetry__extensions__input_completion.proto) | `devtools.jetski.telemetry.extensions` | 2 | 0 | identical |
| [devtools__jetski__telemetry__extensions__monitor_signal.proto](devtools__jetski__telemetry__extensions__monitor_signal.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__read_url_content.proto](devtools__jetski__telemetry__extensions__read_url_content.proto) | `devtools.jetski.telemetry.proto` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__remote_agent_event.proto](devtools__jetski__telemetry__extensions__remote_agent_event.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | differs: 1 definitions only here, 0 only in the desktop backend |
| [devtools__jetski__telemetry__extensions__remote_agent_vm_event.proto](devtools__jetski__telemetry__extensions__remote_agent_vm_event.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__rpc_metadata.proto](devtools__jetski__telemetry__extensions__rpc_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__extensions__state_initialization_data.proto](devtools__jetski__telemetry__extensions__state_initialization_data.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | not in the desktop backend |
| [devtools__jetski__telemetry__extensions__trajectory_segment_events_metadata.proto](devtools__jetski__telemetry__extensions__trajectory_segment_events_metadata.proto) | `devtools.jetski.telemetry.extensions` | 1 | 0 | identical |
| [devtools__jetski__telemetry__telemetry.proto](devtools__jetski__telemetry__telemetry.proto) | `devtools.jetski.telemetry` | 1 | 0 | identical |
| [devtools__search__lang__lang.proto](devtools__search__lang__lang.proto) | `devtools.search` | 0 | 0 | differs: 1 definitions only here, 0 only in the desktop backend |
| [devtools__search__lang__recognize_language.proto](devtools__search__lang__recognize_language.proto) | `codesearch` | 3 | 0 | identical |
| [devtools__staticanalysis__pipeline__analyzers__proto_best_practices__proto__optouts.proto](devtools__staticanalysis__pipeline__analyzers__proto_best_practices__proto__optouts.proto) | `proto_best_practices` | 2 | 0 | identical |
| [frameworks__client__logging__proto__client_event_metadata.proto](frameworks__client__logging__proto__client_event_metadata.proto) | `frameworks.client.logging.proto` | 1 | 0 | identical |
| [frameworks__client__logging__proto__client_event_trace.proto](frameworks__client__logging__proto__client_event_trace.proto) | `frameworks.client.logging.proto` | 1 | 0 | not in the desktop backend |
| [frameworks__client__logging__proto__client_info.proto](frameworks__client__logging__proto__client_info.proto) | `frameworks.client.logging.proto` | 1 | 0 | identical |
| [frameworks__client__logging__proto__client_log_event.proto](frameworks__client__logging__proto__client_log_event.proto) | `frameworks.client.logging.proto` | 1 | 0 | identical |
| [frameworks__client__logging__proto__client_release.proto](frameworks__client__logging__proto__client_release.proto) | `frameworks.client.logging.proto` | 1 | 0 | identical |
| [frameworks__client__logging__proto__parameter.proto](frameworks__client__logging__proto__parameter.proto) | `frameworks.client.logging.proto` | 1 | 0 | identical |
| [frameworks__protobuf__protoui__protoui.proto](frameworks__protobuf__protoui__protoui.proto) | `frameworks.protobuf.protoui` | 4 | 0 | identical |
| [gdata__rosy__proto__data.proto](gdata__rosy__proto__data.proto) | `gdata` | 11 | 0 | identical |
| [gdm__security__agi_control__agent_monitoring__monitors__monitor_config.proto](gdm__security__agi_control__agent_monitoring__monitors__monitor_config.proto) | `gdm.security.agi_control.agent_monitoring.monitors` | 38 | 0 | differs: 14 definitions only here, 1 only in the desktop backend |
| [gmscore__platform__proto__device_types.proto](gmscore__platform__proto__device_types.proto) | `gms.platform` | 0 | 0 | differs: 4 definitions only here, 0 only in the desktop backend |
| [google__api__annotations.proto](google__api__annotations.proto) | `google.api` | 0 | 0 | same schema, different options |
| [google__api__auditing.proto](google__api__auditing.proto) | `google.api` | 2 | 0 | identical |
| [google__api__authz.proto](google__api__authz.proto) | `google.api` | 1 | 0 | same schema, different options |
| [google__api__client.proto](google__api__client.proto) | `google.api` | 16 | 0 | identical |
| [google__api__expr__checked.proto](google__api__expr__checked.proto) | `google.api.expr` | 4 | 0 | identical |
| [google__api__expr__eval.proto](google__api__expr__eval.proto) | `google.api.expr` | 5 | 0 | identical |
| [google__api__expr__explain.proto](google__api__expr__explain.proto) | `google.api.expr` | 1 | 0 | identical |
| [google__api__expr__syntax.proto](google__api__expr__syntax.proto) | `google.api.expr` | 4 | 0 | identical |
| [google__api__expr__v1alpha1__checked.proto](google__api__expr__v1alpha1__checked.proto) | `google.api.expr.v1alpha1` | 4 | 0 | identical |
| [google__api__expr__v1alpha1__eval.proto](google__api__expr__v1alpha1__eval.proto) | `google.api.expr.v1alpha1` | 4 | 0 | identical |
| [google__api__expr__v1alpha1__explain.proto](google__api__expr__v1alpha1__explain.proto) | `google.api.expr.v1alpha1` | 1 | 0 | identical |
| [google__api__expr__v1alpha1__syntax.proto](google__api__expr__v1alpha1__syntax.proto) | `google.api.expr.v1alpha1` | 5 | 0 | identical |
| [google__api__expr__v1alpha1__value.proto](google__api__expr__v1alpha1__value.proto) | `google.api.expr.v1alpha1` | 4 | 0 | identical |
| [google__api__expr__value.proto](google__api__expr__value.proto) | `google.api.expr` | 4 | 0 | identical |
| [google__api__field_behavior.proto](google__api__field_behavior.proto) | `google.api` | 0 | 0 | identical |
| [google__api__field_info.proto](google__api__field_info.proto) | `google.api` | 2 | 0 | identical |
| [google__api__http.proto](google__api__http.proto) | `google.api` | 4 | 0 | identical |
| [google__api__httpbody.proto](google__api__httpbody.proto) | `google.api` | 1 | 0 | identical |
| [google__api__inclusion.proto](google__api__inclusion.proto) | `google.api` | 1 | 0 | identical |
| [google__api__launch_stage.proto](google__api__launch_stage.proto) | `google.api` | 0 | 0 | identical |
| [google__api__policy.proto](google__api__policy.proto) | `google.api` | 5 | 0 | identical |
| [google__api__resource.proto](google__api__resource.proto) | `google.api` | 4 | 0 | identical |
| [google__api__versioning.proto](google__api__versioning.proto) | `google.api` | 2 | 0 | identical |
| [google__api__visibility.proto](google__api__visibility.proto) | `google.api` | 2 | 0 | identical |
| [google__cloud__aiplatform__common__proto_field_options.proto](google__cloud__aiplatform__common__proto_field_options.proto) | `google.cloud.aiplatform.common` | 1 | 0 | identical |
| [google__cloud__aiplatform__master__aai_live_messages.proto](google__cloud__aiplatform__master__aai_live_messages.proto) | `google.cloud.aiplatform.master` | 9 | 0 | identical |
| [google__cloud__aiplatform__master__api_auth.proto](google__cloud__aiplatform__master__api_auth.proto) | `google.cloud.aiplatform.master` | 1 | 0 | identical |
| [google__cloud__aiplatform__master__auth.proto](google__cloud__aiplatform__master__auth.proto) | `google.cloud.aiplatform.master` | 1 | 0 | identical |
| [google__cloud__aiplatform__master__cached_content.proto](google__cloud__aiplatform__master__cached_content.proto) | `google.cloud.aiplatform.master` | 2 | 0 | not in the desktop backend |
| [google__cloud__aiplatform__master__content.proto](google__cloud__aiplatform__master__content.proto) | `google.cloud.aiplatform.master` | 54 | 0 | same schema, different options |
| [google__cloud__aiplatform__master__encryption_spec.proto](google__cloud__aiplatform__master__encryption_spec.proto) | `google.cloud.aiplatform.master` | 1 | 0 | identical |
| [google__cloud__aiplatform__master__explanation.proto](google__cloud__aiplatform__master__explanation.proto) | `google.cloud.aiplatform.master` | 19 | 0 | identical |
| [google__cloud__aiplatform__master__explanation_metadata.proto](google__cloud__aiplatform__master__explanation_metadata.proto) | `google.cloud.aiplatform.master` | 1 | 0 | identical |
| [google__cloud__aiplatform__master__io.proto](google__cloud__aiplatform__master__io.proto) | `google.cloud.aiplatform.master` | 16 | 0 | identical |
| [google__cloud__aiplatform__master__media_metadata.proto](google__cloud__aiplatform__master__media_metadata.proto) | `google.cloud.aiplatform.master` | 3 | 0 | same schema, different options |
| [google__cloud__aiplatform__master__openapi.proto](google__cloud__aiplatform__master__openapi.proto) | `google.cloud.aiplatform.master` | 1 | 0 | identical |
| [google__cloud__aiplatform__master__prediction_service.proto](google__cloud__aiplatform__master__prediction_service.proto) | `google.cloud.aiplatform.master` | 58 | 34 | same schema, different options |
| [google__cloud__aiplatform__master__service_tier.proto](google__cloud__aiplatform__master__service_tier.proto) | `google.cloud.aiplatform.master` | 0 | 0 | identical |
| [google__cloud__aiplatform__master__tool.proto](google__cloud__aiplatform__master__tool.proto) | `google.cloud.aiplatform.master` | 31 | 0 | same schema, different options |
| [google__cloud__aiplatform__master__types.proto](google__cloud__aiplatform__master__types.proto) | `google.cloud.aiplatform.master` | 7 | 0 | same schema, different options |
| [google__cloud__aiplatform__master__vertex_rag_data.proto](google__cloud__aiplatform__master__vertex_rag_data.proto) | `google.cloud.aiplatform.master` | 23 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__aai_live_messages.proto](google__cloud__aiplatform__v1beta1__aai_live_messages.proto) | `google.cloud.aiplatform.v1beta1` | 9 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__agent.proto](google__cloud__aiplatform__v1beta1__agent.proto) | `google.cloud.aiplatform.v1beta1` | 17 | 0 | differs: 1 definitions only here, 0 only in the desktop backend |
| [google__cloud__aiplatform__v1beta1__agent_service.proto](google__cloud__aiplatform__v1beta1__agent_service.proto) | `google.cloud.aiplatform.v1beta1` | 23 | 6 | identical |
| [google__cloud__aiplatform__v1beta1__api_auth.proto](google__cloud__aiplatform__v1beta1__api_auth.proto) | `google.cloud.aiplatform.v1beta1` | 1 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__auth.proto](google__cloud__aiplatform__v1beta1__auth.proto) | `google.cloud.aiplatform.v1beta1` | 1 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__cached_content.proto](google__cloud__aiplatform__v1beta1__cached_content.proto) | `google.cloud.aiplatform.v1beta1` | 2 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__content.proto](google__cloud__aiplatform__v1beta1__content.proto) | `google.cloud.aiplatform.v1beta1` | 52 | 0 | not in the desktop backend |
| [google__cloud__aiplatform__v1beta1__environment.proto](google__cloud__aiplatform__v1beta1__environment.proto) | `google.cloud.aiplatform.v1beta1` | 7 | 0 | not in the desktop backend |
| [google__cloud__aiplatform__v1beta1__explanation_metadata.proto](google__cloud__aiplatform__v1beta1__explanation_metadata.proto) | `google.cloud.aiplatform.v1beta1` | 1 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__io.proto](google__cloud__aiplatform__v1beta1__io.proto) | `google.cloud.aiplatform.v1beta1` | 16 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__media_metadata.proto](google__cloud__aiplatform__v1beta1__media_metadata.proto) | `google.cloud.aiplatform.v1beta1` | 3 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__openapi.proto](google__cloud__aiplatform__v1beta1__openapi.proto) | `google.cloud.aiplatform.v1beta1` | 1 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__operation.proto](google__cloud__aiplatform__v1beta1__operation.proto) | `google.cloud.aiplatform.v1beta1` | 2 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__service_tier.proto](google__cloud__aiplatform__v1beta1__service_tier.proto) | `google.cloud.aiplatform.v1beta1` | 0 | 0 | identical |
| [google__cloud__aiplatform__v1beta1__task.proto](google__cloud__aiplatform__v1beta1__task.proto) | `google.cloud.aiplatform.v1beta1` | 30 | 0 | differs: 8 definitions only here, 0 only in the desktop backend |
| [google__cloud__aiplatform__v1beta1__task_events.proto](google__cloud__aiplatform__v1beta1__task_events.proto) | `google.cloud.aiplatform.v1beta1` | 23 | 0 | differs: 17 definitions only here, 0 only in the desktop backend |
| [google__cloud__aiplatform__v1beta1__task_service.proto](google__cloud__aiplatform__v1beta1__task_service.proto) | `google.cloud.aiplatform.v1beta1` | 36 | 21 | differs: 43 definitions only here, 0 only in the desktop backend |
| [google__cloud__aiplatform__v1beta1__tool.proto](google__cloud__aiplatform__v1beta1__tool.proto) | `google.cloud.aiplatform.v1beta1` | 30 | 0 | same schema, different options |
| [google__cloud__aiplatform__v1beta1__types.proto](google__cloud__aiplatform__v1beta1__types.proto) | `google.cloud.aiplatform.v1beta1` | 5 | 0 | identical |
| [google__cloud__automl__common__proto_field_options.proto](google__cloud__automl__common__proto_field_options.proto) | `google.cloud.automl.common` | 2 | 0 | identical |
| [google__cloud__businessaicode__v1beta__management_service.proto](google__cloud__businessaicode__v1beta__management_service.proto) | `google.cloud.businessaicode.v1beta` | 17 | 5 | identical |
| [google__cloud__businessaicode__v1beta__telemetry_service.proto](google__cloud__businessaicode__v1beta__telemetry_service.proto) | `google.cloud.businessaicode.v1beta` | 7 | 1 | identical |
| [google__cloud__businessaicode__v1main__content.proto](google__cloud__businessaicode__v1main__content.proto) | `google.cloud.businessaicode.v1main` | 41 | 0 | differs: 8 definitions only here, 0 only in the desktop backend |
| [google__cloud__businessaicode__v1main__entitlement.proto](google__cloud__businessaicode__v1main__entitlement.proto) | `google.cloud.businessaicode.v1main` | 1 | 0 | identical |
| [google__cloud__businessaicode__v1main__prediction_service.proto](google__cloud__businessaicode__v1main__prediction_service.proto) | `google.cloud.businessaicode.v1main` | 7 | 3 | identical |
| [google__cloud__speech__v1p1beta1__cloud_speech.proto](google__cloud__speech__v1p1beta1__cloud_speech.proto) | `google.cloud.speech.v1p1beta1` | 24 | 3 | identical |
| [google__cloud__speech__v1p1beta1__resource.proto](google__cloud__speech__v1p1beta1__resource.proto) | `google.cloud.speech.v1p1beta1` | 4 | 0 | identical |
| [google__gca__aicode__v1main__management_service.proto](google__gca__aicode__v1main__management_service.proto) | `google.gca.aicode.v1main` | 12 | 5 | differs: 10 definitions only here, 0 only in the desktop backend |
| [google__gca__aicode__v1main__quota_status.proto](google__gca__aicode__v1main__quota_status.proto) | `google.gca.aicode.v1main` | 3 | 0 | identical |
| [google__gca__aicode__v1main__remote_agent_proxy.proto](google__gca__aicode__v1main__remote_agent_proxy.proto) | `google.gca.aicode.v1main` | 345 | 141 | differs: 341 definitions only here, 21 only in the desktop backend |
| [google__internal__cloud__code__v1internal__cloudcode.proto](google__internal__cloud__code__v1internal__cloudcode.proto) | `google.internal.cloud.code.v1internal` | 69 | 23 | same schema, different options |
| [google__internal__cloud__code__v1internal__credits.proto](google__internal__cloud__code__v1internal__credits.proto) | `google.internal.cloud.code.v1internal` | 1 | 0 | not in the desktop backend |
| [google__internal__cloud__code__v1internal__entitlement.proto](google__internal__cloud__code__v1internal__entitlement.proto) | `google.internal.cloud.code.v1internal` | 1 | 0 | identical |
| [google__internal__cloud__code__v1internal__jetski_service.proto](google__internal__cloud__code__v1internal__jetski_service.proto) | `google.internal.cloud.code.v1internal` | 62 | 21 | differs: 15 definitions only here, 0 only in the desktop backend |
| [google__internal__cloud__code__v1internal__metrics.proto](google__internal__cloud__code__v1internal__metrics.proto) | `google.internal.cloud.code.v1internal` | 12 | 0 | identical |
| [google__internal__cloud__code__v1internal__model_configs.proto](google__internal__cloud__code__v1internal__model_configs.proto) | `google.internal.cloud.code.v1internal` | 13 | 0 | differs: 1 definitions only here, 0 only in the desktop backend |
| [google__internal__cloud__code__v1internal__onboarding.proto](google__internal__cloud__code__v1internal__onboarding.proto) | `google.internal.cloud.code.v1internal` | 20 | 0 | identical |
| [google__internal__cloud__code__v1internal__prediction_service.proto](google__internal__cloud__code__v1internal__prediction_service.proto) | `google.internal.cloud.code.v1internal` | 6 | 6 | identical |
| [google__internal__cloud__code__v1internal__quota_summary.proto](google__internal__cloud__code__v1internal__quota_summary.proto) | `google.internal.cloud.code.v1internal` | 5 | 0 | differs: 12 definitions only here, 0 only in the desktop backend |
| [google__internal__cloud__code__v1internal__remote_context.proto](google__internal__cloud__code__v1internal__remote_context.proto) | `google.internal.cloud.code.v1internal` | 2 | 0 | identical |
| [google__internal__identity__signedoutstate__v1__signed_out_state_container.proto](google__internal__identity__signedoutstate__v1__signed_out_state_container.proto) | `google.internal.identity.signedoutstate.v1` | 1 | 0 | identical |
| [google__longrunning__operations.proto](google__longrunning__operations.proto) | `google.longrunning` | 8 | 5 | identical |
| [google__protobuf__any.proto](google__protobuf__any.proto) | `google.protobuf` | 1 | 0 | identical |
| [google__protobuf__duration.proto](google__protobuf__duration.proto) | `google.protobuf` | 1 | 0 | identical |
| [google__protobuf__empty.proto](google__protobuf__empty.proto) | `google.protobuf` | 1 | 0 | identical |
| [google__protobuf__field_mask.proto](google__protobuf__field_mask.proto) | `google.protobuf` | 1 | 0 | identical |
| [google__protobuf__struct.proto](google__protobuf__struct.proto) | `google.protobuf` | 3 | 0 | identical |
| [google__protobuf__timestamp.proto](google__protobuf__timestamp.proto) | `google.protobuf` | 1 | 0 | same schema, different options |
| [google__protobuf__wrappers.proto](google__protobuf__wrappers.proto) | `google.protobuf` | 9 | 0 | identical |
| [google__rpc__code.proto](google__rpc__code.proto) | `google.rpc` | 0 | 0 | identical |
| [google__rpc__context__abuse_context.proto](google__rpc__context__abuse_context.proto) | `google.rpc.context` | 1 | 0 | identical |
| [google__rpc__context__apiary_migration_context.proto](google__rpc__context__apiary_migration_context.proto) | `google.rpc.context` | 1 | 0 | identical |
| [google__rpc__context__check_context.proto](google__rpc__context__check_context.proto) | `google.rpc.context` | 1 | 0 | identical |
| [google__rpc__context__condition_context.proto](google__rpc__context__condition_context.proto) | `google.rpc.context` | 3 | 0 | identical |
| [google__rpc__context__field_mask_context.proto](google__rpc__context__field_mask_context.proto) | `google.rpc.context` | 1 | 0 | identical |
| [google__rpc__context__http_header_context.proto](google__rpc__context__http_header_context.proto) | `google.rpc.context` | 1 | 0 | identical |
| [google__rpc__context__origin_context.proto](google__rpc__context__origin_context.proto) | `google.rpc.context` | 1 | 0 | identical |
| [google__rpc__context__project_context.proto](google__rpc__context__project_context.proto) | `google.rpc.context` | 2 | 0 | identical |
| [google__rpc__context__system_parameter_context.proto](google__rpc__context__system_parameter_context.proto) | `google.rpc.context` | 1 | 0 | identical |
| [google__rpc__context__visibility_context.proto](google__rpc__context__visibility_context.proto) | `google.rpc.context` | 1 | 0 | identical |
| [google__rpc__error_details.proto](google__rpc__error_details.proto) | `google.rpc` | 10 | 0 | identical |
| [google__rpc__status.proto](google__rpc__status.proto) | `google.rpc` | 1 | 0 | identical |
| [google__type__date.proto](google__type__date.proto) | `google.type` | 1 | 0 | identical |
| [google__type__interval.proto](google__type__interval.proto) | `google.type` | 1 | 0 | identical |
| [google__type__latlng.proto](google__type__latlng.proto) | `google.type` | 1 | 0 | identical |
| [identity__ubermint__proto__platform_options.proto](identity__ubermint__proto__platform_options.proto) | `ubermint.options` | 0 | 0 | identical |
| [identity__ubermint__proto__universe.proto](identity__ubermint__proto__universe.proto) | `ubermint` | 1 | 0 | identical |
| [java__com__google__android__gmscore__proto__gmscore_device.proto](java__com__google__android__gmscore__proto__gmscore_device.proto) | `com.google.android.gmscore.proto` | 1 | 0 | differs: 1 definitions only here, 0 only in the desktop backend |
| [java__com__google__apps__jspb__jspb.proto](java__com__google__apps__jspb__jspb.proto) | `jspb` | 0 | 0 | identical |
| [java__com__google__apps__jspb__jspb_disable_randomization.proto](java__com__google__apps__jspb__jspb_disable_randomization.proto) | `jspb` | 0 | 0 | identical |
| [java__com__google__protobuf__contrib__j2cl__options__js_enum.proto](java__com__google__protobuf__contrib__j2cl__options__js_enum.proto) | `protobuf.contrib.j2cl.options` | 0 | 0 | identical |
| [learning__genai__api__interactions__proto__agent_tools.proto](learning__genai__api__interactions__proto__agent_tools.proto) | `genai` | 11 | 0 | same schema, different options |
| [learning__genai__api__interactions__proto__agents.proto](learning__genai__api__interactions__proto__agents.proto) | `genai` | 8 | 0 | not in the desktop backend |
| [learning__genai__api__interactions__proto__elicitation.proto](learning__genai__api__interactions__proto__elicitation.proto) | `genai` | 9 | 0 | differs: 0 definitions only here, 22 only in the desktop backend |
| [learning__genai__api__interactions__proto__environment.proto](learning__genai__api__interactions__proto__environment.proto) | `genai` | 3 | 0 | differs: 0 definitions only here, 29 only in the desktop backend |
| [learning__genai__api__interactions__proto__error.proto](learning__genai__api__interactions__proto__error.proto) | `genai` | 2 | 0 | same schema, different options |
| [learning__genai__api__interactions__proto__external_events.proto](learning__genai__api__interactions__proto__external_events.proto) | `genai` | 5 | 0 | differs: 13 definitions only here, 2 only in the desktop backend |
| [learning__genai__api__interactions__proto__interaction_service.proto](learning__genai__api__interactions__proto__interaction_service.proto) | `genai` | 0 | 3 | differs: 0 definitions only here, 2 only in the desktop backend |
| [learning__genai__api__interactions__proto__media_stream_steps.proto](learning__genai__api__interactions__proto__media_stream_steps.proto) | `genai` | 3 | 0 | differs: 0 definitions only here, 5 only in the desktop backend |
| [learning__genai__api__interactions__proto__models.proto](learning__genai__api__interactions__proto__models.proto) | `genai` | 10 | 0 | differs: 0 definitions only here, 74 only in the desktop backend |
| [learning__genai__api__interactions__proto__remote_environment.proto](learning__genai__api__interactions__proto__remote_environment.proto) | `genai` | 4 | 0 | same schema, different options |
| [learning__genai__api__interactions__proto__response_format.proto](learning__genai__api__interactions__proto__response_format.proto) | `genai` | 6 | 0 | differs: 0 definitions only here, 11 only in the desktop backend |
| [learning__genai__api__interactions__proto__safety_settings.proto](learning__genai__api__interactions__proto__safety_settings.proto) | `genai` | 1 | 0 | differs: 0 definitions only here, 6 only in the desktop backend |
| [learning__genai__api__interactions__proto__sse_events.proto](learning__genai__api__interactions__proto__sse_events.proto) | `genai` | 51 | 0 | differs: 4 definitions only here, 18 only in the desktop backend |
| [learning__genai__api__interactions__proto__state_update.proto](learning__genai__api__interactions__proto__state_update.proto) | `genai` | 3 | 0 | not in the desktop backend |
| [learning__genai__api__interactions__proto__steps.proto](learning__genai__api__interactions__proto__steps.proto) | `genai` | 55 | 0 | differs: 2 definitions only here, 72 only in the desktop backend |
| [learning__genai__api__interactions__proto__thinking.proto](learning__genai__api__interactions__proto__thinking.proto) | `genai` | 0 | 0 | differs: 0 definitions only here, 5 only in the desktop backend |
| [learning__genai__api__interactions__proto__tools.proto](learning__genai__api__interactions__proto__tools.proto) | `genai` | 14 | 0 | not in the desktop backend |
| [learning__genai__api__interactions__proto__user_action_input.proto](learning__genai__api__interactions__proto__user_action_input.proto) | `genai` | 1 | 0 | same schema, different options |
| [learning__genai__api__interactions__proto__user_steps.proto](learning__genai__api__interactions__proto__user_steps.proto) | `genai` | 2 | 0 | differs: 1 definitions only here, 10 only in the desktop backend |
| [learning__genai__api__localharness__localharness.proto](learning__genai__api__localharness__localharness.proto) | `antigravity.localharness` | 110 | 0 | differs: 5 definitions only here, 95 only in the desktop backend |
| [learning__genai__api__parsing__genai_json_annotations.proto](learning__genai__api__parsing__genai_json_annotations.proto) | `gaos.parsing` | 1 | 0 | identical |
| [learning__genai__beyond__server__quota__proto__quota.proto](learning__genai__beyond__server__quota__proto__quota.proto) | `beyond` | 3 | 0 | identical |
| [learning__genai__skills__proto__skill.proto](learning__genai__skills__proto__skill.proto) | `genai.skills` | 2 | 0 | differs: 0 definitions only here, 3 only in the desktop backend |
| [logs__eventid__eventid.proto](logs__eventid__eventid.proto) | `` | 2 | 0 | same schema, different options |
| [logs__java__logrecord.proto](logs__java__logrecord.proto) | `` | 5 | 0 | identical |
| [logs__proto__learning__smartchoices__feedback_id.proto](logs__proto__learning__smartchoices__feedback_id.proto) | `smartchoices` | 1 | 0 | identical |
| [logs__proto__logs_annotations__logs_annotations.proto](logs__proto__logs_annotations__logs_annotations.proto) | `logs_proto` | 1 | 0 | not in the desktop backend |
| [logs__proto__visual_element__user_action_enum.proto](logs__proto__visual_element__user_action_enum.proto) | `logs` | 0 | 0 | not in the desktop backend |
| [logs__proto__wireless__android__play__playlog__accessibility__ios_accessibility_state.proto](logs__proto__wireless__android__play__playlog__accessibility__ios_accessibility_state.proto) | `wireless_android_play_playlog.accessibility` | 1 | 0 | identical |
| [logs__proto__wireless__android__play__playlog__webview_version_info.proto](logs__proto__wireless__android__play__playlog__webview_version_info.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [logs__proto__wireless__performance__mobile__android__trace_record.proto](logs__proto__wireless__performance__mobile__android__trace_record.proto) | `logs.proto.wireless.performance.mobile.android` | 3 | 0 | identical |
| [logs__proto__wireless__performance__mobile__annotations.proto](logs__proto__wireless__performance__mobile__annotations.proto) | `logs.proto.wireless.performance.mobile` | 1 | 0 | same schema, different options |
| [logs__proto__wireless__performance__mobile__application_exit_metric.proto](logs__proto__wireless__performance__mobile__application_exit_metric.proto) | `logs.proto.wireless.performance.mobile` | 6 | 0 | identical |
| [logs__proto__wireless__performance__mobile__battery_metric.proto](logs__proto__wireless__performance__mobile__battery_metric.proto) | `logs.proto.wireless.performance.mobile` | 12 | 0 | identical |
| [logs__proto__wireless__performance__mobile__cpu_profiling.proto](logs__proto__wireless__performance__mobile__cpu_profiling.proto) | `logs.proto.wireless.performance.mobile` | 3 | 0 | identical |
| [logs__proto__wireless__performance__mobile__cui_metadata.proto](logs__proto__wireless__performance__mobile__cui_metadata.proto) | `logs.proto.wireless.performance.mobile` | 1 | 0 | identical |
| [logs__proto__wireless__performance__mobile__desktop__antigravity_extension.proto](logs__proto__wireless__performance__mobile__desktop__antigravity_extension.proto) | `logs.proto.wireless.performance.mobile.desktop` | 1 | 0 | same schema, different options |
| [logs__proto__wireless__performance__mobile__desktop__desktop_system_health_metric.proto](logs__proto__wireless__performance__mobile__desktop__desktop_system_health_metric.proto) | `logs.proto.wireless.performance.mobile.desktop` | 2 | 0 | identical |
| [logs__proto__wireless__performance__mobile__desktop__trace_record.proto](logs__proto__wireless__performance__mobile__desktop__trace_record.proto) | `logs.proto.wireless.performance.mobile.desktop` | 1 | 0 | identical |
| [logs__proto__wireless__performance__mobile__extension_metric.proto](logs__proto__wireless__performance__mobile__extension_metric.proto) | `logs.proto.wireless.performance.mobile` | 1 | 0 | same schema, different options |
| [logs__proto__wireless__performance__mobile__heap_profile_metric.proto](logs__proto__wireless__performance__mobile__heap_profile_metric.proto) | `logs.proto.wireless.performance.mobile` | 1 | 0 | identical |
| [logs__proto__wireless__performance__mobile__interaction_context.proto](logs__proto__wireless__performance__mobile__interaction_context.proto) | `logs.proto.wireless.performance.mobile` | 1 | 0 | identical |
| [logs__proto__wireless__performance__mobile__ios__nserror.proto](logs__proto__wireless__performance__mobile__ios__nserror.proto) | `logs.proto.wireless.performance.mobile.ios` | 2 | 0 | identical |
| [logs__proto__wireless__performance__mobile__memory_metric.proto](logs__proto__wireless__performance__mobile__memory_metric.proto) | `logs.proto.wireless.performance.mobile` | 4 | 0 | identical |
| [logs__proto__wireless__performance__mobile__native_crash_info.proto](logs__proto__wireless__performance__mobile__native_crash_info.proto) | `logs.proto.wireless.performance.mobile` | 1 | 0 | identical |
| [logs__proto__wireless__performance__mobile__network_metric.proto](logs__proto__wireless__performance__mobile__network_metric.proto) | `logs.proto.wireless.performance.mobile` | 7 | 0 | identical |
| [logs__proto__wireless__performance__mobile__primes_trace.proto](logs__proto__wireless__performance__mobile__primes_trace.proto) | `logs.proto.wireless.performance.mobile` | 5 | 0 | identical |
| [logs__proto__wireless__performance__mobile__process_stats.proto](logs__proto__wireless__performance__mobile__process_stats.proto) | `logs.proto.wireless.performance.mobile` | 3 | 0 | identical |
| [logs__proto__wireless__performance__mobile__sampling.proto](logs__proto__wireless__performance__mobile__sampling.proto) | `logs.proto.wireless.performance.mobile` | 1 | 0 | not in the desktop backend |
| [logs__proto__wireless__performance__mobile__trace_context.proto](logs__proto__wireless__performance__mobile__trace_context.proto) | `logs.proto.wireless.performance.mobile` | 2 | 0 | identical |
| [logs__proto__wireless__performance__mobile__tracing.proto](logs__proto__wireless__performance__mobile__tracing.proto) | `logs.proto.wireless.performance.mobile` | 4 | 0 | not in the desktop backend |
| [nest__platform__mesh__platform__proto__platform_information_no_lite_runtime.proto](nest__platform__mesh__platform__proto__platform_information_no_lite_runtime.proto) | `smart_home_mesh.platform` | 0 | 0 | identical |
| [net__base__qos.proto](net__base__qos.proto) | `net_base.qos` | 1 | 0 | identical |
| [net__loadshedding__proto__request_qos.proto](net__loadshedding__proto__request_qos.proto) | `net.loadshedding` | 1 | 0 | identical |
| [net__proto2__bridge__proto__message_set.proto](net__proto2__bridge__proto__message_set.proto) | `proto2.bridge` | 1 | 0 | identical |
| [net__proto2__contrib__parse_proto__parse_error.proto](net__proto2__contrib__parse_proto__parse_error.proto) | `proto2.contrib.parse_proto` | 1 | 0 | not in the desktop backend |
| [net__proto2__contrib__validator__annotations.proto](net__proto2__contrib__validator__annotations.proto) | `validator` | 4 | 0 | identical |
| [net__proto2__go__annotation__go_annotation.proto](net__proto2__go__annotation__go_annotation.proto) | `go_annotation` | 0 | 0 | identical |
| [net__proto2__proto__descriptor.proto](net__proto2__proto__descriptor.proto) | `proto2` | 23 | 0 | identical |
| [perftools__tracing__proto__rpc_trace_info.proto](perftools__tracing__proto__rpc_trace_info.proto) | `perftools.tracing` | 7 | 0 | not in the desktop backend |
| [perftools__tracing__proto__span_link.proto](perftools__tracing__proto__span_link.proto) | `perftools.tracing` | 2 | 0 | not in the desktop backend |
| [perftools__tracing__proto__tracecontext.proto](perftools__tracing__proto__tracecontext.proto) | `perftools.tracing` | 1 | 0 | not in the desktop backend |
| [privacy__ads__user_data_enforcement__data-usage.proto](privacy__ads__user_data_enforcement__data-usage.proto) | `privacy.ads.user_data_enforcement` | 1 | 0 | identical |
| [privacy__context__external__proto__external_privacy_context.proto](privacy__context__external__proto__external_privacy_context.proto) | `privacy.context.external` | 1 | 0 | identical |
| [privacy__context__external__proto__external_sot_mappings.proto](privacy__context__external__proto__external_sot_mappings.proto) | `privacy.context.external` | 1 | 0 | identical |
| [privacy__core__piihandling__protosanitizer__config__sanitization_use_cases.proto](privacy__core__piihandling__protosanitizer__config__sanitization_use_cases.proto) | `privacy.core.piihandling.protosanitizer.config` | 2 | 0 | identical |
| [privacy__data_governance__attributes__proto__classification.proto](privacy__data_governance__attributes__proto__classification.proto) | `privacy.data_governance.attributes.classification` | 1 | 0 | identical |
| [privacy__data_governance__attributes__proto__purpose.proto](privacy__data_governance__attributes__proto__purpose.proto) | `privacy.data_governance.attributes` | 1 | 0 | not in the desktop backend |
| [privacy__pattributes__annotations__proto_field.proto](privacy__pattributes__annotations__proto_field.proto) | `pattributes` | 0 | 0 | identical |
| [privacy__pattributes__containers__proto_field__proto_field_attributes.proto](privacy__pattributes__containers__proto_field__proto_field_attributes.proto) | `pattributes` | 1 | 0 | same schema, different options |
| [privacy__pattributes__containers__proto_field__proto_field_upload_justification.proto](privacy__pattributes__containers__proto_field__proto_field_upload_justification.proto) | `pattributes` | 1 | 0 | identical |
| [privacy__pattributes__public__proto__collection_basis.proto](privacy__pattributes__public__proto__collection_basis.proto) | `pattributes` | 0 | 0 | identical |
| [privacy__pattributes__public__proto__collection_basis_expression.proto](privacy__pattributes__public__proto__collection_basis_expression.proto) | `pattributes` | 4 | 0 | identical |
| [search__wga__proto__logged_enums.proto](search__wga__proto__logged_enums.proto) | `wga` | 0 | 0 | identical |
| [security__credentials__proto__authenticator.proto](security__credentials__proto__authenticator.proto) | `security.credentials` | 40 | 0 | identical |
| [security__credentials__proto__data_access_token_scope.proto](security__credentials__proto__data_access_token_scope.proto) | `security.credentials` | 1 | 0 | identical |
| [security__credentials__proto__justification.proto](security__credentials__proto__justification.proto) | `security.credentials` | 2 | 0 | identical |
| [security__credentials__proto__principal.proto](security__credentials__proto__principal.proto) | `security.credentials` | 31 | 0 | identical |
| [security__data_access__proto__standard_dat_scope.proto](security__data_access__proto__standard_dat_scope.proto) | `security.data_access` | 4 | 0 | identical |
| [security__loas__l2__internal__securewrapper__multihop_clients__boundary_proxy.proto](security__loas__l2__internal__securewrapper__multihop_clients__boundary_proxy.proto) | `boundary_proxy.proto_modifier` | 1 | 0 | identical |
| [security__thinmint__proto__core__thinmint_core.proto](security__thinmint__proto__core__thinmint_core.proto) | `thinmint` | 1 | 0 | identical |
| [security__zatar__s2a__proto__common.proto](security__zatar__s2a__proto__common.proto) | `s2a.proto` | 2 | 0 | identical |
| [security__zatar__s2a__proto__s2a.proto](security__zatar__s2a__proto__s2a.proto) | `s2a.proto` | 10 | 1 | identical |
| [security__zatar__s2a__proto__s2a_context.proto](security__zatar__s2a__proto__s2a_context.proto) | `s2a.proto` | 1 | 0 | identical |
| [storage__datapol__annotations__mapping__sensitivity_levels.proto](storage__datapol__annotations__mapping__sensitivity_levels.proto) | `datapol.mapping` | 0 | 0 | identical |
| [storage__datapol__annotations__proto__datapol_classification.proto](storage__datapol__annotations__proto__datapol_classification.proto) | `datapol.classification` | 1 | 0 | identical |
| [storage__datapol__annotations__proto__retention_annotations.proto](storage__datapol__annotations__proto__retention_annotations.proto) | `datapol` | 1 | 0 | identical |
| [storage__datapol__annotations__proto__semantic_annotations.proto](storage__datapol__annotations__proto__semantic_annotations.proto) | `datapol` | 6 | 0 | differs: 1 definitions only here, 0 only in the desktop backend |
| [storage__googlesql__public__proto__type_annotation.proto](storage__googlesql__public__proto__type_annotation.proto) | `googlesql` | 2 | 0 | identical |
| [storage__googlesql__public__proto__wire_format_annotation.proto](storage__googlesql__public__proto__wire_format_annotation.proto) | `googlesql` | 1 | 0 | identical |
| [third_party__boundary_proxy__proto_compare__annotation.proto](third_party__boundary_proxy__proto_compare__annotation.proto) | `proto_compare` | 2 | 0 | identical |
| [third_party__gemini_coder__cider__proto__trajectory_steps.proto](third_party__gemini_coder__cider__proto__trajectory_steps.proto) | `devtools.cider.agent.trajectory_steps` | 17 | 0 | identical |
| [third_party__gemini_coder__framework__executor__agentexecutor__proto__inputs.proto](third_party__gemini_coder__framework__executor__agentexecutor__proto__inputs.proto) | `gemini_coder.agentexecutor` | 4 | 0 | differs: 0 definitions only here, 1 only in the desktop backend |
| [third_party__gemini_coder__proto__service.proto](third_party__gemini_coder__proto__service.proto) | `gemini_coder` | 22 | 0 | identical |
| [third_party__gemini_coder__proto__trajectory.proto](third_party__gemini_coder__proto__trajectory.proto) | `gemini_coder` | 5 | 0 | identical |
| [third_party__gloop__thread__fiber__fiber.proto](third_party__gloop__thread__fiber__fiber.proto) | `thread.fiber` | 0 | 0 | not in the desktop backend |
| [third_party__gloop__util__status__non_message_set_payload.proto](third_party__gloop__util__status__non_message_set_payload.proto) | `util` | 1 | 0 | identical |
| [third_party__golang__connectrpc_com__connect__v__v1__internal__proto__connectext__grpc__status__v1__status.proto](third_party__golang__connectrpc_com__connect__v__v1__internal__proto__connectext__grpc__status__v1__status.proto) | `grpc.status.v1` | 1 | 0 | identical |
| [third_party__golang__protobuf__v2__src__google__protobuf__go_features.proto](third_party__golang__protobuf__v2__src__google__protobuf__go_features.proto) | `pb` | 1 | 0 | identical |
| [third_party__grpc_proto__grpc__binlog__v1__binarylog.proto](third_party__grpc_proto__grpc__binlog__v1__binarylog.proto) | `grpc.binarylog.v1` | 8 | 0 | identical |
| [third_party__java__protobuf__java_features.proto](third_party__java__protobuf__java_features.proto) | `pb` | 1 | 0 | identical |
| [third_party__jetski__actuator_pb__actuator.proto](third_party__jetski__actuator_pb__actuator.proto) | `exa.actuator_pb` | 17 | 4 | differs: 1 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__api_server_pb__api_server.proto](third_party__jetski__api_server_pb__api_server.proto) | `exa.api_server_pb` | 234 | 114 | differs: 0 definitions only here, 5 only in the desktop backend |
| [third_party__jetski__browser_pb__browser.proto](third_party__jetski__browser_pb__browser.proto) | `exa.browser_pb` | 2 | 0 | identical |
| [third_party__jetski__browser_pb__webview_browser.proto](third_party__jetski__browser_pb__webview_browser.proto) | `exa.browser_pb` | 1 | 0 | identical |
| [third_party__jetski__cascade_plugins_pb__cascade_plugins.proto](third_party__jetski__cascade_plugins_pb__cascade_plugins.proto) | `exa.cascade_plugins_pb` | 13 | 3 | identical |
| [third_party__jetski__chat_client_server_pb__chat_client_server.proto](third_party__jetski__chat_client_server_pb__chat_client_server.proto) | `exa.chat_client_server_pb` | 6 | 1 | identical |
| [third_party__jetski__chat_pb__chat.proto](third_party__jetski__chat_pb__chat.proto) | `exa.chat_pb` | 37 | 0 | differs: 0 definitions only here, 3 only in the desktop backend |
| [third_party__jetski__codeium_common_pb__codeium_common.proto](third_party__jetski__codeium_common_pb__codeium_common.proto) | `exa.codeium_common_pb` | 258 | 0 | differs: 9 definitions only here, 6 only in the desktop backend |
| [third_party__jetski__config_pb__config.proto](third_party__jetski__config_pb__config.proto) | `exa.config_pb` | 8 | 0 | differs: 4 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__context_module_pb__context_module.proto](third_party__jetski__context_module_pb__context_module.proto) | `exa.context_module_pb` | 20 | 0 | identical |
| [third_party__jetski__cortex__core__native_swarm__proto__model_descriptions.proto](third_party__jetski__cortex__core__native_swarm__proto__model_descriptions.proto) | `exa.cortex.native_swarm` | 3 | 0 | identical |
| [third_party__jetski__cortex__core__tools_pb__tools.proto](third_party__jetski__cortex__core__tools_pb__tools.proto) | `exa.cortex.core.tools` | 17 | 0 | not in the desktop backend |
| [third_party__jetski__cortex__ext__plugins__browser__browserpb__browser_payload.proto](third_party__jetski__cortex__ext__plugins__browser__browserpb__browser_payload.proto) | `exa.browser_payload_pb` | 6 | 0 | differs: 8 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__cortex__ext__plugins__computer__computerpb__computer_payload.proto](third_party__jetski__cortex__ext__plugins__computer__computerpb__computer_payload.proto) | `exa.computer_payload_pb` | 14 | 0 | identical |
| [third_party__jetski__cortex__ext__plugins__computer__computerpb__computer_viewer.proto](third_party__jetski__cortex__ext__plugins__computer__computerpb__computer_viewer.proto) | `exa.computer_payload_pb` | 9 | 0 | identical |
| [third_party__jetski__cortex__modelapi__proto__safety_signals_extension.proto](third_party__jetski__cortex__modelapi__proto__safety_signals_extension.proto) | `exa.cortex_pb` | 0 | 0 | identical |
| [third_party__jetski__cortex__policyguardian__state.proto](third_party__jetski__cortex__policyguardian__state.proto) | `jetski.cortex.policyguardian` | 5 | 0 | differs: 3 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__cortex_pb__cortex.proto](third_party__jetski__cortex_pb__cortex.proto) | `exa.cortex_pb` | 514 | 0 | differs: 96 definitions only here, 13 only in the desktop backend |
| [third_party__jetski__cortex_pb__custom_extensions.proto](third_party__jetski__cortex_pb__custom_extensions.proto) | `exa.cortex_pb` | 1 | 0 | identical |
| [third_party__jetski__cortex_pb__options.proto](third_party__jetski__cortex_pb__options.proto) | `exa.cortex_pb` | 0 | 0 | identical |
| [third_party__jetski__dev_pb__dev.proto](third_party__jetski__dev_pb__dev.proto) | `exa.dev_pb` | 2 | 1 | identical |
| [third_party__jetski__diff_action_pb__diff_action.proto](third_party__jetski__diff_action_pb__diff_action.proto) | `exa.diff_action_pb` | 8 | 0 | identical |
| [third_party__jetski__hooks_pb__hooks.proto](third_party__jetski__hooks_pb__hooks.proto) | `exa.hooks_pb` | 25 | 0 | differs: 1 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__host_bridge_pb__host_bridge.proto](third_party__jetski__host_bridge_pb__host_bridge.proto) | `exa.host_bridge_pb` | 8 | 3 | identical |
| [third_party__jetski__index_pb__index.proto](third_party__jetski__index_pb__index.proto) | `exa.index_pb` | 62 | 23 | identical |
| [third_party__jetski__jetbox_state_pb__jetbox_state.proto](third_party__jetski__jetbox_state_pb__jetbox_state.proto) | `jetbox_state_pb` | 19 | 0 | differs: 6 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__jetbox_summaries_pb__jetbox_summaries.proto](third_party__jetski__jetbox_summaries_pb__jetbox_summaries.proto) | `jetbox_summaries_pb` | 1 | 0 | identical |
| [third_party__jetski__jetski_cortex_pb__jetski_cortex.proto](third_party__jetski__jetski_cortex_pb__jetski_cortex.proto) | `exa.jetski_cortex_pb` | 49 | 0 | differs: 4 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__language_server__browserviewerpb__browser_viewer.proto](third_party__jetski__language_server__browserviewerpb__browser_viewer.proto) | `exa.browser_viewer_payload_pb` | 6 | 0 | not in the desktop backend |
| [third_party__jetski__language_server__browserviewerpb__browser_viewer_service.proto](third_party__jetski__language_server__browserviewerpb__browser_viewer_service.proto) | `exa.browser_viewer_pb` | 18 | 9 | not in the desktop backend |
| [third_party__jetski__language_server__computerviewerpb__computer_viewer_service.proto](third_party__jetski__language_server__computerviewerpb__computer_viewer_service.proto) | `exa.computer_viewer_pb` | 11 | 5 | not in the desktop backend |
| [third_party__jetski__language_server_pb__language_server.proto](third_party__jetski__language_server_pb__language_server.proto) | `exa.language_server_pb` | 769 | 327 | differs: 22 definitions only here, 398 only in the desktop backend |
| [third_party__jetski__plugin_context_pb__plugin_context.proto](third_party__jetski__plugin_context_pb__plugin_context.proto) | `exa.plugin_context_pb` | 9 | 3 | identical |
| [third_party__jetski__product_api_pb__v1__conversation_service.proto](third_party__jetski__product_api_pb__v1__conversation_service.proto) | `jetski.product.v1` | 8 | 5 | identical |
| [third_party__jetski__project_pb__project.proto](third_party__jetski__project_pb__project.proto) | `exa.project_pb` | 16 | 0 | identical |
| [third_party__jetski__prompt_pb__prompt.proto](third_party__jetski__prompt_pb__prompt.proto) | `exa.prompt_pb` | 6 | 0 | identical |
| [third_party__jetski__reactive_component_pb__reactive_component.proto](third_party__jetski__reactive_component_pb__reactive_component.proto) | `exa.reactive_component_pb` | 10 | 0 | identical |
| [third_party__jetski__remote_agent_proxy_pb__remote_agent_proxy.proto](third_party__jetski__remote_agent_proxy_pb__remote_agent_proxy.proto) | `exa.remote_agent_proxy_pb` | 14 | 0 | differs: 46 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__remote_agent_proxy_pb__voice.proto](third_party__jetski__remote_agent_proxy_pb__voice.proto) | `exa.remote_agent_proxy_pb` | 7 | 0 | differs: 1 definitions only here, 0 only in the desktop backend |
| [third_party__jetski__remote_agent_proxy_pb__voice_trajectory.proto](third_party__jetski__remote_agent_proxy_pb__voice_trajectory.proto) | `exa.remote_agent_proxy_pb` | 11 | 0 | identical |
| [third_party__jetski__remoting_pb__remoting.proto](third_party__jetski__remoting_pb__remoting.proto) | `exa.remoting` | 16 | 7 | identical |
| [third_party__jetski__seat_management_pb__seat_management.proto](third_party__jetski__seat_management_pb__seat_management.proto) | `exa.seat_management_pb` | 215 | 96 | identical |
| [third_party__jetski__vcs_pb__vcs.proto](third_party__jetski__vcs_pb__vcs.proto) | `exa.vcs_pb` | 7 | 0 | identical |
| [third_party__prometheus_model__metrics.proto](third_party__prometheus_model__metrics.proto) | `io.prometheus.client` | 12 | 0 | identical |
| [third_party__protobuf__cpp_features.proto](third_party__protobuf__cpp_features.proto) | `pb` | 1 | 0 | identical |
| [third_party__protobuf__cpp_file_options.proto](third_party__protobuf__cpp_file_options.proto) | `pb.file` | 1 | 0 | identical |
| [third_party__protobuf__internal_options.proto](third_party__protobuf__internal_options.proto) | `pb` | 3 | 0 | identical |
| [third_party__protobuf__json_enumvalue_options.proto](third_party__protobuf__json_enumvalue_options.proto) | `pb.enumvalue` | 1 | 0 | identical |
| [third_party__s2a_proto__s2a__proto__v2__common.proto](third_party__s2a_proto__s2a__proto__v2__common.proto) | `s2a.proto.v2` | 2 | 0 | not in the desktop backend |
| [third_party__s2a_proto__s2a__proto__v2__s2a.proto](third_party__s2a_proto__s2a__proto__v2__s2a.proto) | `s2a.proto.v2` | 13 | 1 | identical |
| [third_party__s2a_proto__s2a__proto__v2__s2a_context.proto](third_party__s2a_proto__s2a__proto__v2__s2a_context.proto) | `s2a.proto.v2` | 1 | 0 | identical |
| [third_party__tcmalloc__internal__mallocz.proto](third_party__tcmalloc__internal__mallocz.proto) | `tcmalloc.tcmalloc_internal.perftools.profiles` | 30 | 0 | not in the desktop backend |
| [util__task__codes.proto](util__task__codes.proto) | `util.error` | 0 | 0 | identical |
| [util__task__error_space_payload.proto](util__task__error_space_payload.proto) | `util` | 1 | 0 | identical |
| [util__task__stack_trace.proto](util__task__stack_trace.proto) | `util` | 2 | 0 | identical |
| [util__task__status.proto](util__task__status.proto) | `util` | 1 | 0 | identical |
| [video__youtube__logs__proto__datapol_annotations.proto](video__youtube__logs__proto__datapol_annotations.proto) | `youtube_logs` | 0 | 0 | not in the desktop backend |
| [wireless__android__play__playlog__proto__clearcut_logger_remote_config_metadata.proto](wireless__android__play__playlog__proto__clearcut_logger_remote_config_metadata.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__android.proto](wireless__android__play__playlog__proto__client_info__android.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__android_device_type.proto](wireless__android__play__playlog__proto__client_info__android_device_type.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__battlestar.proto](wireless__android__play__playlog__proto__client_info__battlestar.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__birdsong.proto](wireless__android__play__playlog__proto__client_info__birdsong.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__desktop.proto](wireless__android__play__playlog__proto__client_info__desktop.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__desktop_services.proto](wireless__android__play__playlog__proto__client_info__desktop_services.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__drive_desktop.proto](wireless__android__play__playlog__proto__client_info__drive_desktop.proto) | `wireless_android_play_playlog` | 1 | 0 | not in the desktop backend |
| [wireless__android__play__playlog__proto__client_info__g1_desktop.proto](wireless__android__play__playlog__proto__client_info__g1_desktop.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__google_home.proto](wireless__android__play__playlog__proto__client_info__google_home.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__ios.proto](wireless__android__play__playlog__proto__client_info__ios.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__kaios.proto](wireless__android__play__playlog__proto__client_info__kaios.proto) | `wireless_android_play_playlog` | 1 | 0 | same schema, different options |
| [wireless__android__play__playlog__proto__client_info__mac.proto](wireless__android__play__playlog__proto__client_info__mac.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__nearby.proto](wireless__android__play__playlog__proto__client_info__nearby.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__nestcam_photon.proto](wireless__android__play__playlog__proto__client_info__nestcam_photon.proto) | `wireless_android_play_playlog` | 1 | 0 | not in the desktop backend |
| [wireless__android__play__playlog__proto__client_info__pancetta.proto](wireless__android__play__playlog__proto__client_info__pancetta.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__play_ce.proto](wireless__android__play__playlog__proto__client_info__play_ce.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__sparklight.proto](wireless__android__play__playlog__proto__client_info__sparklight.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__vr.proto](wireless__android__play__playlog__proto__client_info__vr.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__wga.proto](wireless__android__play__playlog__proto__client_info__wga.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__windows.proto](wireless__android__play__playlog__proto__client_info__windows.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__client_info__yeti.proto](wireless__android__play__playlog__proto__client_info__yeti.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__compliance.proto](wireless__android__play__playlog__proto__compliance.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__device_restriction_enum.proto](wireless__android__play__playlog__proto__device_restriction_enum.proto) | `wireless_android_play_playlog` | 1 | 0 | identical |
| [wireless__android__play__playlog__proto__log_source_enum.proto](wireless__android__play__playlog__proto__log_source_enum.proto) | `wireless_android_play_playlog.LogSourceEnum` | 0 | 0 | differs: 6 definitions only here, 0 only in the desktop backend |
| [wireless__android__play__playlog__proto__logger_enum.proto](wireless__android__play__playlog__proto__logger_enum.proto) | `wireless_android_play_playlog.logger_enum` | 0 | 0 | identical |
| [wireless__android__privacy__annotations__proto__collection_basis_annotations.proto](wireless__android__privacy__annotations__proto__collection_basis_annotations.proto) | `wireless.android.privacy` | 4 | 0 | identical |
| [wireless__android__privacy__annotations__proto__collection_basis_annotations_enums.proto](wireless__android__privacy__annotations__proto__collection_basis_annotations_enums.proto) | `wireless.android.privacy` | 3 | 0 | identical |
| [wireless__android__telemetry__pitot__service__enum_extensions.proto](wireless__android__telemetry__pitot__service__enum_extensions.proto) | `android.telemetry.pitot` | 0 | 0 | identical |
