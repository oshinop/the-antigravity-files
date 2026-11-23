# Server switches

`agy` asks Google's server for these at every start (`listExperiments`). The response held 635 switches. They are grouped by what their names say they control; the meaning column is filled in only where it's established from the client code or its behavior. Long values are cut; the full response is [experiments.json](experiments.json).

| Area | Switches |
|---|---|
| [Code Assist, Gemini CLI & other Google products](#code-assist-gemini-cli--other-google-products) | 193 |
| [Tools, plugins & integrations](#tools-plugins--integrations) | 116 |
| [Agents, sessions & workflows](#agents-sessions--workflows) | 90 |
| [Safety, permissions & data collection](#safety-permissions--data-collection) | 73 |
| [Models, prompts & context](#models-prompts--context) | 60 |
| [Interface, onboarding & promotion](#interface-onboarding--promotion) | 57 |
| [Infrastructure, quotas & other](#infrastructure-quotas--other) | 46 |

## Code Assist, Gemini CLI & other Google products

| Switch | Value | Meaning |
|---|---|---|
| `ApigeeCloudCode__enable_mock_server` | `true` |  |
| `ApigeeGeminiChatTool__enable_apigee_gemini_chat_tool` | `true` |  |
| `Chat__chat_context_window_size` | `-1` |  |
| `Chat__code_customization_enable_learn_more_message` | `true` |  |
| `Chat__delete_chat_request_button` | `true` |  |
| `Chat__delete_response_after_stop` | `true` |  |
| `Chat__display_prompt_recitations` | `true` |  |
| `Chat__edit_chat_request_button` | `true` |  |
| `Chat__enable_agent_mode_slash_commands` | `true` |  |
| `Chat__enable_agentic_chat_ij` | `true` |  |
| `Chat__enable_async_chat_intent_classification` | `false` |  |
| `Chat__enable_chat_agentic_mcp_chat` | `false` |  |
| `Chat__enable_chat_checkpoints` | `true` |  |
| `Chat__enable_chat_crescendo_agents` | `true` |  |
| `Chat__enable_chat_folder_context_selection` | `false` |  |
| `Chat__enable_chat_gemini_cli` | `true` |  |
| `Chat__enable_chat_intent_classification` | `false` |  |
| `Chat__enable_chat_moa` | `false` |  |
| `Chat__enable_chat_named_entity_recognition` | `false` |  |
| `Chat__enable_chat_rag_remote_repositories_context_selection` | `true` |  |
| `Chat__enable_chat_streaming` | `true` |  |
| `Chat__enable_code_customization_webview` | `true` |  |
| `Chat__enable_custom_prompts` | `true` |  |
| `Chat__enable_full_codebase_awareness_chat` | `true` |  |
| `Chat__enable_gemini3_announcement` | `true` |  |
| `Chat__enable_local_codebase_awareness_chat_ij` | `true` |  |
| `Chat__enable_mcp_server` | `false` |  |
| `Chat__enable_mcp_server_ij` | `true` |  |
| `Chat__enable_sessions` | `true` |  |
| `Chat__enable_stream_generate_content_ij` | `false` |  |
| `Chat__enable_suggested_prompts` | `false` |  |
| `Chat__enable_text_snippets` | `true` |  |
| `Chat__enable_workspace_change_in_chat_history` | `true` |  |
| `Chat__fca_chat_context_window_size` | `3500000` |  |
| `Chat__lca_chat_context_window_size_ij` | `3500000` |  |
| `Chat__local_bm25_chat_max_results` | `10` |  |
| `Chat__local_bm25_chat_tokenizer` | `wald_word3` |  |
| `Chat__local_bm25_index_max_files` | `25000` |  |
| `Chat__stop_chat_request_button` | `true` |  |
| `CliComplexityBasedRouting__enabled` | `true` |  |
| `CliComplexityBasedRouting__prompt_complexity` | `90` |  |
| `DatacloudBigqueryGraph__enable_bigquery_graph` | `true` |  |
| `DatacloudDatabases__discoverability_improvements` | `true` |  |
| `DatacloudDatabases__enable_cloud_sql_built_in_user` | `true` |  |
| `DatacloudDatabases__enable_data_preview` | `true` |  |
| `DatacloudDatabases__enable_query_execution_details` | `true` |  |
| `DatacloudDatabases__enable_query_settings_improvements` | `true` |  |
| `DatacloudDataplex__enable_alloydb_adapter` | `true` |  |
| `DatacloudDataplex__enable_biglake_adapter` | `true` |  |
| `DatacloudDataplex__enable_bigquery_adapter` | `true` |  |
| `DatacloudDataplex__enable_bigtable_adapter` | `true` |  |
| `DatacloudDataplex__enable_cloud_sql_adapter` | `true` |  |
| `DatacloudDataplex__enable_firestore_adapter` | `true` |  |
| `DatacloudDataplex__enable_lakehouse_public_data` | `true` |  |
| `DatacloudDataplex__enable_spanner_adapter` | `true` |  |
| `DatacloudFeedback__enable_listnr_survey` | `true` |  |
| `DatacloudKnowledgeCatalog__enable_data_products` | `true` |  |
| `DatacloudKnowledgeCatalog__enable_post_next_improvements` | `true` |  |
| `DatacloudKnowledgeCatalog__enable_relationship_graph_details_panel` | `true` |  |
| `DatacloudMcp__enable_stdio_mcp_proxy` | `true` |  |
| `DatacloudSpark__enable_jupyter_token_broker` | `true` |  |
| `DatacloudSpark__enable_load_in_spark_dataframe_from_gcs` | `true` |  |
| `DatacloudSpark__enable_runtime_profile` | `true` |  |
| `DatacloudSpark__enable_spark_in_sql_editor` | `true` |  |
| `DatacloudTheme__enable_theme_v2` | `false` |  |
| `DuetAICodeTransformIj__enable_ij` | `true` |  |
| `DuetAICodeTransform__custom_slash_commands` | `true` |  |
| `DuetAICodeTransform__display_prompt_recitations` | `true` |  |
| `DuetAICodeTransform__enable_inline_diff` | `true` |  |
| `DuetAICodeTransform__enable_m2` | `true` |  |
| `DuetAiCloudCodeAPI__enable_cloudcode_api` | `true` |  |
| `DuetAiCompletion__adaptingCache_maxInflightRequests` | `2` |  |
| `DuetAiCompletion__codeCompletion_client_side_context_size_limit` | `35000` |  |
| `DuetAiCompletion__codeCompletion_enableAdaptingCache` | `false` |  |
| `DuetAiCompletion__codeCompletion_enableInfixCache` | `false` |  |
| `DuetAiCompletion__codeCompletion_enablePrefetchNextSuggestions` | `true` |  |
| `DuetAiCompletion__codeCompletion_triggerForDeletion` | `false` |  |
| `DuetAiGenerationAndCompletion__track_suffix_length` | `false` |  |
| `DuetAiGeneration__auto_trigger_on_empty_class_struct_def` | `true` |  |
| `DuetAiGeneration__codeGeneration_context_window_size` | `64000` |  |
| `DuetAiGeneration__codeGeneration_enable_codelens_call_to_action` | `false` |  |
| `DuetAiGeneration__codeGeneration_enable_diff_view` | `false` |  |
| `DuetAiGeneration__codeGeneration_enable_new_cy_vsc_ux` | `false` |  |
| `DuetAiGeneration__codeGeneration_enable_pane_view` | `false` |  |
| `DuetAiGeneration__codeGeneration_enable_quickpick_chat` | `true` |  |
| `DuetAiGeneration__codeGeneration_pane_view_default_config` | `false` |  |
| `DuetAiGeneration__codeGeneration_use_transform_api` | `false` |  |
| `DuetAiLocalRag__bm25_in_completion_max_results` | `15` |  |
| `DuetAiLocalRag__cache_co_located` | `20` |  |
| `DuetAiLocalRag__cache_file_limit` | `4194300` |  |
| `DuetAiLocalRag__cache_total_files` | `250` |  |
| `DuetAiLocalRag__enable_local_rag` | `true` |  |
| `DuetAiLocalRag__enable_local_rag_chat` | `true` |  |
| `DuetAiLocalRag__enable_local_rag_completion` | `true` |  |
| `DuetAiLocalRag__enable_local_rag_completion_snippets` | `false` |  |
| `DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning` | `false` |  |
| `DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning_bm25_scoring` | `false` |  |
| `DuetAiLocalRag__enable_local_rag_completion_snippets_with_pruning_colocated_files` | `false` |  |
| `DuetAiLocalRag__enable_wald_file_selection` | `false` |  |
| `DuetAiLocalRag__include_currently_open_files` | `true` |  |
| `DuetAiLocalRag__include_doc_files` | `false` |  |
| `DuetAiLocalRag__include_unit_test_files` | `false` |  |
| `DuetAiLocalRag__local_rag_fragmentation_algorithm` | `wholefile` |  |
| `DuetAiLocalRag__local_rag_reranking_by_language` | `0` |  |
| `DuetAiLocalRag__local_rag_tokenization_algorithm` | `whitespace` |  |
| `DuetAiLocalRag__max_file_search_depth` | `2` |  |
| `DuetAiLocalRag__merge_fragments` | `true` |  |
| `DuetAiLocalRag__otherfiles_chat_limit` | `100` |  |
| `DuetAiLocalRag__otherfiles_completion_limit` | `15` |  |
| `DuetAiLocalRag__otherfiles_generation_limit` | `40` |  |
| `DuetAiLocalRag__sliding_window_fragmenter_stride` | `10` |  |
| `DuetAiLocalRag__sliding_window_fragmenter_window_size` | `20` |  |
| `DuetAiLocalRag__substrings_to_identify_doc_prompts` | `{"values": ["document", "comment"]}` |  |
| `DuetAiLocalRag__substrings_to_identify_test_prompts` | `{"values": ["test"]}` |  |
| `DuetAiLocalRag__top_k_doc_files_to_include` | `2` |  |
| `DuetAiLocalRag__top_k_test_files_to_include` | `2` |  |
| `DuetAiLocalRag__wald_local_rag_max_file_search_depth` | `-1` |  |
| `DuetAiMendelOverrides__chat_clientId` | `CHAT_CLIENT_CLOUD_CODE_GEMINI_2_0_FLASH_001` |  |
| `DuetAiMendelOverrides__enable_gca_intent_classification_as_default_model` | `false` |  |
| `DuetAiMendelOverrides__gca_intent_classifier_model_name` | `/ml/m2p-role-prod-intentclassifiergca-servo-owner/prod.inten…` |  |
| `DuetAiMendelOverrides__gca_intent_classifier_model_version` | `` |  |
| `DuetAiMendelOverrides__inlineSuggestions_debounceMs` | `300` |  |
| `DuetAiMendelOverrides__inlineSuggestions_debounced_after_fetching` | `false` |  |
| `DuetAiMendelOverrides__inlineSuggestions_throttleMs` | `0` |  |
| `DuetAiProcessors__enable_prompt_recitations_check` | `true` |  |
| `DuetAiRemoteRag__enable_hyde_for_generation` | `false` |  |
| `DuetAiRemoteRag__enable_remote_rag` | `true` |  |
| `DuetAiRemoteRag__enable_remote_rag_chat` | `true` |  |
| `DuetAiRemoteRag__max_bm25_snippets_rag` | `0` |  |
| `DuetAiRemoteRag__max_distance_rag_for_chat` | `0.8` |  |
| `DuetAiRemoteRag__max_distance_rag_for_selected_code` | `0.8` |  |
| `DuetAiRemoteRag__max_distance_tailed_prompt` | `0.4` |  |
| `DuetAiRemoteRag__max_named_entity_for_chat` | `3` |  |
| `DuetAiRemoteRag__max_snippets_rag_for_chat` | `2` |  |
| `DuetAiRemoteRag__max_snippets_rag_for_selected_code` | `2` |  |
| `DuetAiRemoteRag__max_snippets_tailed_prompt` | `4` |  |
| `DuetAiRemoteRag__multi_query_tail_ns_for_completion` | `{}` |  |
| `DuetAiRemoteRag__multi_query_tail_ns_for_generation` | `{}` |  |
| `DuetAi__custom_preambles` | `true` |  |
| `FirebaseDataConnectChatTool__enable_firebase_data_connect_chat_tool` | `false` |  |
| `GCAFeedbackBlock__enable_feedback_block` | `false` |  |
| `GCAUpgradeToPaid__current_tier_polling_interval_ms` | `43200000` |  |
| `GCAUpgradeToPaid__enable_g1_upgrade_flow` | `false` |  |
| `GCAUpgradeToPaid__enable_upgrade_from_free_tier` | `true` |  |
| `GcaAipluginSwingToCompose__enable_compose` | `false` |  |
| `GcaApiMigration__platform_api_chat_experience` | `` |  |
| `GcaApiMigration__platform_api_completion_experience` | `` |  |
| `GcaApiMigration__platform_api_generation_experience` | `` |  |
| `GcaApiMigration__platform_api_transformation_experience` | `` |  |
| `GcaApiMigration__product_api_chat_experience` | `` |  |
| `GcaApiMigration__product_api_completion_experience` | `` |  |
| `GcaApiMigration__product_api_generation_experience` | `` |  |
| `GcaApiMigration__product_api_transformation_experience` | `` |  |
| `GcaCitationBlock__enable_citation_block` | `false` |  |
| `GcaEventsPipeline__enable_events_pipeline_polling` | `false` |  |
| `GcaEventsPipeline__events_pipeline_polling_interval_ms` | `60000` |  |
| `GcaFlashCompletions__completion_replaces_cursor_line` | `false` |  |
| `GcaFlashCompletions__enable_client_postprocessing` | `false` |  |
| `GcaFlashCompletions__enable_flash_completions` | `false` |  |
| `GcaTransformFinishChanges__enable_finish_changes` | `false` |  |
| `GcaTransformOutlines__enable_automatic_generation` | `false` |  |
| `GcaTransformOutlines__enable_outlines` | `true` |  |
| `GcaUx__enable_gm3_design_system` | `true` |  |
| `GcliAgentHistoryTruncation__agent_history_retained_messages` | `15` |  |
| `GcliAgentHistoryTruncation__agent_history_truncation_threshold` | `30` |  |
| `GcliAgentHistoryTruncation__enable_agent_history_truncation` | `false` |  |
| `GcliCompressionPrompt__compression_prompt` | `` |  |
| `GcliConfigPayload__config_payload` | `` |  |
| `GcliConfig__cli_max_attempts` | `10` |  |
| `GeminiCLIBannerText__capacity_issues` | `` |  |
| `GeminiCLIBannerText__no_capacity_issues` | `` |  |
| `GeminiCLIContextCompression__threshold_fraction` | `0` |  |
| `GeminiCLIIsLaunched__gemini_3_1_flash_lite_ga_launched` | `true` |  |
| `GeminiCLIIsLaunched__gemini_3_1_flash_lite_preview_launched` | `true` |  |
| `GeminiCLIIsLaunched__gemini_3_1_flash_preview_launched` | `true` |  |
| `GeminiCLIIsLaunched__gemini_3_1_pro_preview_launched` | `true` |  |
| `GeminiCLIIsLaunched__gemini_3_8_flash_launched` | `true` |  |
| `GeminiCLIIsLaunched__gemini_3_flash_ga_launched` | `true` |  |
| `GeminiCLIIsLaunched__gemini_3_pro_launched` | `true` |  |
| `GeminiCLIPreviewAvailable__enable_preview` | `true` |  |
| `GeminiFreeTier__call_onboard_user_from_legacy_flow` | `true` |  |
| `GeminiFreeTier__enable_free_tier` | `false` |  |
| `GeminiFreeTier__license_message_frequency_days` | `60` |  |
| `IntentAware__ellipsis` | `false` |  |
| `IntentAware__enable_intent_aware_m1` | `true` |  |
| `SDLCAgents__enable_anthropic_model_connection` | `false` |  |
| `SDLCAgents__enable_azure_model_connection` | `false` |  |
| `SDLCAgents__enable_gemini_model_connection` | `false` |  |
| `SDLCAgents__enable_rest_model_connection` | `false` |  |
| `SparkMonitorIntegration__enable_spark_monitor_integration` | `true` |  |
| `SyntaxAnalysis__enable_syntax_analysis` | `false` |  |
| `Timeouts__cli_request_timeout_seconds` | `300` |  |
| `Timeouts__cli_total_request_timeout_seconds` | `600` |  |

## Tools, plugins & integrations

| Switch | Value | Meaning |
|---|---|---|
| `1p-projects-v5` | `false` |  |
| `CASCADE_USE_REPLACE_CONTENT_EDIT_TOOL` | `{"max_fuzzy_edit_distance_fraction": 0.001, "allow_partial_r…` |  |
| `agy-featured-plugins` | `` |  |
| `agy-plugin-marketplaces` | `{"values": ["antigravity-plugins-official=https://www.gstati…` |  |
| `agy-plugin-shelves` | `{"shelves": [{   "id": "google-workspace",   "displayName": …` |  |
| `cascade-use-sed-edit-tool` | `false` |  |
| `cli-enable-search-everything` | `true` |  |
| `default-magic-workspace-vcs-jj` | `false` |  |
| `disable-search-tools` | `false` |  |
| `disable-tool-hook-notices` | `false` |  |
| `enable-abfs-workspace-ui` | `false` |  |
| `enable-agy-hub-cua-foreground` | `false` |  |
| `enable-agy-hub-cua-macos` | `false` |  |
| `enable-agy-hub-cua-script-mode` | `true` |  |
| `enable-ark` | `false` |  |
| `enable-artifact-inline-follow-up` | `false` |  |
| `enable-ask-question-tool` | `true` |  |
| `enable-audio-transcription` | `false` |  |
| `enable-bottom-terminal-panel` | `false` |  |
| `enable-browser-element-picker` | `false` |  |
| `enable-citc-workspace-hygiene-rule` | `true` |  |
| `enable-code-search-palette` | `false` |  |
| `enable-customization-kind-flow` | `false` |  |
| `enable-customization-kind-hooks` | `true` |  |
| `enable-customization-kind-mcp` | `true` |  |
| `enable-customization-kind-plugin` | `true` |  |
| `enable-customization-kind-rule` | `true` |  |
| `enable-customization-kind-skill` | `true` |  |
| `enable-customization-load-error-notice` | `false` |  |
| `enable-customization-skills` | `true` |  |
| `enable-daemon-commands` | `false` |  |
| `enable-deferred-tool-loading` | `false` |  |
| `enable-dictation-rework` | `false` |  |
| `enable-directory-local-discovery` | `false` |  |
| `enable-fig-vcs-ui` | `true` |  |
| `enable-generative-hooks` | `false` |  |
| `enable-get-screenshot-data` | `false` |  |
| `enable-git-vcs-ui` | `false` |  |
| `enable-github-decorator` | `false` |  |
| `enable-gob-cog-vcs-ui` | `true` |  |
| `enable-gob-repo-vcs-ui` | `true` |  |
| `enable-hook-status` | `true` |  |
| `enable-hub-builtin-browser` | `false` |  |
| `enable-ide-audio-transcription` | `false` |  |
| `enable-jetbox-terminal-v2` | `false` |  |
| `enable-jj-vcs-ui` | `false` |  |
| `enable-lazy-loaded-projects` | `false` |  |
| `enable-list-plugin-accounts-tool` | `false` |  |
| `enable-live-browser-aux-pane` | `false` |  |
| `enable-marketplace-new-badge` | `false` |  |
| `enable-marketplace-search-tool` | `false` |  |
| `enable-marketplace-user-count` | `false` |  |
| `enable-mcp-apps` | `false` |  |
| `enable-mcp-non-blocking-turn-load` | `true` |  |
| `enable-mcp-oauth-redirect` | `false` |  |
| `enable-mcp-plugin-auth` | `true` |  |
| `enable-notebook-rendering` | `false` |  |
| `enable-omni-mention-search` | `false` |  |
| `enable-pdf-viewer` | `false` |  |
| `enable-persistent-terminals` | `true` |  |
| `enable-plugin-auth` | `true` |  |
| `enable-plugin-bundles` | `false` |  |
| `enable-plugin-card-auto-resume` | `false` |  |
| `enable-plugin-ranking` | `false` |  |
| `enable-plugin-sidebar-section` | `false` |  |
| `enable-plugin-themes` | `false` |  |
| `enable-plugin-updates-menu` | `false` |  |
| `enable-plugins` | `false` |  |
| `enable-product-api-skill` | `false` |  |
| `enable-programmatic-tools` | `false` |  |
| `enable-project-4k` | `false` |  |
| `enable-pseudo-project` | `true` |  |
| `enable-pty` | `true` |  |
| `enable-remote-jj-commit-graph` | `false` |  |
| `enable-remote-local-browser-hands` | `false` |  |
| `enable-remote-terminal-predictive-echo` | `true` |  |
| `enable-retroactive-projects-migration` | `true` |  |
| `enable-ripgrep-path-search` | `true` |  |
| `enable-sidecar-pill-auto-open` | `false` |  |
| `enable-sidecar-sdk` | `false` |  |
| `enable-sidecars` | `false` |  |
| `enable-sidecars-user-config-migration-ready` | `false` |  |
| `enable-skill-accumulator` | `false` |  |
| `enable-skill-icons` | `false` |  |
| `enable-skill-search-tool` | `false` |  |
| `enable-ssh-remote` | `false` |  |
| `enable-terminal-setup-script` | `false` |  |
| `enable-ui-sidecars` | `false` |  |
| `enable-update-focus-tool` | `false` |  |
| `enable-update-steps-tool` | `false` |  |
| `enable-upload-html-artifact-to-drive` | `false` |  |
| `enable-url-artifacts` | `false` |  |
| `enable-versioned-plugin-dirs` | `false` |  |
| `enable-voice-relay` | `true` |  |
| `enable-voice-remote` | `false` |  |
| `enable-voice-spanner` | `false` |  |
| `enable-wait-tool` | `false` |  |
| `google-workspace-mentions` | `false` |  |
| `jetski-featured-plugins` | `` |  |
| `jetski-plugin-promotions` | `` |  |
| `jetski-plugin-shelves` | `` |  |
| `jetski-unified-customizations-panel-enabled` | `false` |  |
| `json-hooks-enabled` | `true` |  |
| `max-media-attachments` | `15` |  |
| `max_injected_skills` | `-1` |  |
| `mcp-lazy-load-tools` | `true` |  |
| `media-compression-config` | `` |  |
| `pin-depot-head` | `false` |  |
| `project-4k-mode-copy` | `` |  |
| `science-plugin-split-targets` | `{}` |  |
| `sidecar-gateway` | `false` |  |
| `sidecar-iframe-prewarm` | `false` |  |
| `tool-output-max-bytes` | `10240` |  |
| `turn-on-projects-internally` | `true` |  |
| `use_slope_gated_skills` | `false` |  |
| `voice-engine-addr` | `default` |  |

## Agents, sessions & workflows

| Switch | Value | Meaning |
|---|---|---|
| `agent-retry-config` | `` |  |
| `agent-script-reroute` | `` |  |
| `battle-mode-max-turns` | `0` |  |
| `battle-winner-survey-percent` | `50` |  |
| `cascade-agent-api-config` | `` |  |
| `cascade-enable-messaging` | `true` |  |
| `cli-disable-stream-conversation-updates-rehydration` | `false` |  |
| `cli-enable-agent-view` | `false` |  |
| `cli-enable-conversation-groups` | `false` |  |
| `convenient-multi-agent-coordination` | `false` |  |
| `deepagent-arm` | `` |  |
| `default_subagent_interaction_timeout_seconds` | `60` |  |
| `deprecate-workflows` | `false` |  |
| `enable-advanced-builtin-workflow` | `false` |  |
| `enable-agent-plugins-spec` | `true` |  |
| `enable-agent-plugins-spec-1-1` | `false` |  |
| `enable-agent-team` | `false` |  |
| `enable-battle-contrib-variants` | `false` |  |
| `enable-battle-mode` | `false` |  |
| `enable-battle-mode-custom-agents` | `true` |  |
| `enable-browser-subagent-auto-invoke` | `false` |  |
| `enable-browser-subagent-glic-host` | `false` |  |
| `enable-browser-subagent-inline-pdf` | `false` |  |
| `enable-browser-subagent-milestone-collab` | `false` |  |
| `enable-browser-subagent-url-binding` | `false` |  |
| `enable-browser-subagent-v2` | `true` |  |
| `enable-browser-subagent-webmcp` | `false` |  |
| `enable-browser-subagent-webview` | `false` |  |
| `enable-browser-subagent-webview-script` | `false` |  |
| `enable-browser-subagent-webview-ux` | `false` |  |
| `enable-concierge-agent` | `false` |  |
| `enable-conversation-forking` | `false` |  |
| `enable-conversation-groups` | `false` |  |
| `enable-conversation-navigator` | `false` |  |
| `enable-conversation-navigator-chapter-titles` | `true` |  |
| `enable-conversation-only-revert` | `false` |  |
| `enable-conversation-project-migration` | `true` |  |
| `enable-conversation-search-v2` | `false` |  |
| `enable-conversation-selection-quoting` | `false` |  |
| `enable-conversation-sharing-prototype` | `false` |  |
| `enable-critique-decorator` | `false` |  |
| `enable-critique-preview` | `false` |  |
| `enable-customization-kind-agent` | `true` |  |
| `enable-customization-kind-workflow` | `true` |  |
| `enable-deepagent` | `false` |  |
| `enable-fork-artifact-streaming` | `false` |  |
| `enable-fork-at-historical-step` | `false` |  |
| `enable-fork-in-new-abfs-workspace` | `false` |  |
| `enable-fork-in-new-citc-workspace` | `false` |  |
| `enable-fork-in-new-worktree` | `false` |  |
| `enable-fork-provenance` | `false` |  |
| `enable-fork-with-subagents` | `false` |  |
| `enable-image-generator-subagent` | `true` |  |
| `enable-jetski-web-coordinators` | `false` |  |
| `enable-markdown-agents` | `true` |  |
| `enable-move-conversation-to-project` | `false` |  |
| `enable-native-swarm` | `true` |  |
| `enable-remote-battle-mode` | `false` |  |
| `enable-remote-conversation-live-browser` | `false` |  |
| `enable-remote-conversation-navigator` | `false` |  |
| `enable-remote-conversation-preview` | `false` |  |
| `enable-remote-conversation-terminal` | `false` |  |
| `enable-remote-conversation-vm` | `false` |  |
| `enable-remote-coordinators` | `false` |  |
| `enable-remote-persistent-agents` | `false` |  |
| `enable-remote-personal-agents` | `false` |  |
| `enable-remote-preview-wake` | `true` |  |
| `enable-remote-standalone-conversations` | `false` |  |
| `enable-remote-team-agents` | `false` |  |
| `enable-resume-conversations-from-restart` | `false` |  |
| `enable-review` | `false` |  |
| `enable-run-workflow-terminal-return` | `false` |  |
| `enable-run-workflow-tool` | `true` |  |
| `enable-subagent-hub` | `true` |  |
| `enable-subagent-user-messaging` | `false` |  |
| `enable-swarm-graph-tab` | `true` |  |
| `enable-swarm-narrator` | `false` |  |
| `enable-swarm-overview` | `false` |  |
| `enable-swarm-raw-tab` | `false` |  |
| `enable-tasks` | `true` |  |
| `enable-teamwork-subagent` | `true` | Whether `/teamwork` is offered |
| `enable-trajectory-debug-in-battle-mode` | `false` |  |
| `invoke-subagent-config` | `{"enabled": true, "always_inherit_model": false}` | Switches on the prompt's subagent sections |
| `max-conversation-save-count` | `500` |  |
| `native-swarm-channels-local` | `false` |  |
| `native-swarm-channels-spanner` | `false` |  |
| `native-swarm-spanner-board` | `false` |  |
| `review-arm` | `` |  |
| `scheduled-tasks-v2` | `false` |  |
| `verifiers_experiment_config` | `` |  |

## Safety, permissions & data collection

| Switch | Value | Meaning |
|---|---|---|
| `CodeassistMetrics__enable_codeassist_metric` | `false` |  |
| `DuetAiMendelOverrides__enable_gca_intent_classification_model_for_logging` | `false` |  |
| `GcaTelemetryBlock__enable_telemetry_block` | `false` |  |
| `GcaTelemetry__enable_ai_characters_percentage` | `true` |  |
| `GcaTelemetry__send_aica_to_ccpa` | `true` |  |
| `MetricService__enable_metric_service` | `true` |  |
| `UserTelemetry__enable_user_telemetry_call` | `true` |  |
| `allow-experimental-monitors` | `false` |  |
| `allow-pss-experimental-monitors` | `false` |  |
| `ambient_subagent_read_only` | `false` |  |
| `apply-trajectory-acls` | `true` |  |
| `auto-command-config` | `{"system_allowlist": ["cd"], "sandbox_system_allowlist": ["h…` | Commands that run without review, inside and outside the sandbox |
| `auto-mode-permission-preset` | `auto` | The permission preset auto mode uses |
| `cli-enable-vetted-permission` | `false` |  |
| `cli-suggest-auto-mode-in-permission-prompt` | `false` |  |
| `disable-command-assessor` | `false` |  |
| `disable-loop-detection` | `true` |  |
| `disable-stained-loas2-creds` | `true` |  |
| `enable-agent-omnitrace` | `false` |  |
| `enable-ask-custom-permission-tool` | `false` |  |
| `enable-ask-permission-tool` | `false` |  |
| `enable-auto-permission-preset` | `true` |  |
| `enable-autonomous-mode-critic` | `false` |  |
| `enable-browser-site-data-controls` | `false` |  |
| `enable-browser-subagent-high-stakes-consent` | `false` |  |
| `enable-browser-subagent-sensitive-site-ask` | `false` |  |
| `enable-browser-subagent-url-safety` | `false` |  |
| `enable-citc-top-level-write-guard` | `false` |  |
| `enable-command-assessor` | `true` |  |
| `enable-conversation-memory-monitor` | `false` |  |
| `enable-conversation-scoped-permissions` | `false` |  |
| `enable-gil-logging` | `false` |  |
| `enable-per-conversation-telemetry-toggle` | `false` |  |
| `enable-permissioned-github` | `false` |  |
| `enable-permissions-selector` | `false` |  |
| `enable-primes-tracing` | `false` |  |
| `enable-remote-agent-uberproxy-sso` | `false` |  |
| `enable-remote-control-audio-transcription` | `true` |  |
| `enable-remote-control-hub-browser-stream` | `false` |  |
| `enable-remote-credentials-for-local-plugins` | `false` |  |
| `enable-rpc-sandbox-proxy` | `false` |  |
| `enable-rpc-sandbox-proxy-allow-all` | `false` | Whether the sandbox's network proxy lets all traffic through |
| `enable-run-workflow-sandbox` | `false` |  |
| `enable-scoped-csrf-tokens` | `true` |  |
| `enable-swarm-monitor` | `true` |  |
| `enable-trustedcli-config-files` | `false` |  |
| `enable-unified-approved-sites` | `false` |  |
| `enable-voice-remote-control` | `false` |  |
| `enable-workspace-trust` | `false` |  |
| `enforce-default-permission-preset` | `false` | Whether the default permission preset is enforced |
| `enforce-telemetry-disabled` | `false` |  |
| `enroll-new-users-in-auto-mode` | `false` | Whether new users start in auto mode |
| `google3-default-org-policy` | `` |  |
| `jetski-autonomous-mode` | `false` |  |
| `jetski-shared-beyond-session-id` | `false` |  |
| `jetski-sidecar-declared-permissions` | `false` |  |
| `log-artifacts-config` | `{"enabled": true, "hideNominalToolSteps": false, "hidePlanne…` | What gets logged, and switches on the prompt's `<artifacts>` section |
| `permission-v2` | `true` |  |
| `plugin-kill-switch` | `` |  |
| `policy-guardian-config` | `{"allowed": true, "deployment": "ANTIGRAVITY", "enable_irrev…` | Configures the safety reviewer and turns on auto mode's reversibility adjudicator |
| `record-error-sampling-rates` | `{       "error_match_to_sample_rate": {         "LS_CRASH": …` |  |
| `remote-control-companion-url` | `https://antigravity.google.com` |  |
| `remote-control-proxy-server-url` | `jetski-webchannel.googleapis.com:443` |  |
| `remote-control-setting-enabled` | `true` | Whether `agy`'s remote-control feature is on |
| `remote-control-silence-timeout-seconds` | `60` |  |
| `remote-control-version` | `v2` |  |
| `remote-control-webrtc-mesh` | `true` |  |
| `rpc-telemetry-allowlist` | `{}` |  |
| `show-trajectory-labeling-ad` | `false` |  |
| `sonar-active-tags` | `` |  |
| `sonar-monitors-config` | `` |  |
| `sso-based-user-identity` | `false` |  |
| `third-party-tools-disclaimer-text` | `{           "default": "Google does not control the availabi…` |  |

## Models, prompts & context

| Switch | Value | Meaning |
|---|---|---|
| `ambient_disabled_models` | `fable-5.2-*,claude-fable-5-2*` |  |
| `ambient_injection_enabled` | `true` |  |
| `ambient_persistent_injection_enabled` | `false` |  |
| `ambient_prefetch_enabled` | `false` |  |
| `ambient_query_max_thought_chars` | `0` |  |
| `ambient_query_max_thought_steps` | `0` |  |
| `best-of-n-default-model-id` | `` |  |
| `best-of-nudge-dismiss-cooldown-days` | `10` |  |
| `best-of-nudge-length-threshold` | `150` |  |
| `browser-subagent-model` | `gemini-3.8-flash-low` |  |
| `cascade-conversation-history-config` | `{"enabled": true, "max_conversations": 20}` |  |
| `cascade-knowledge-config` | `{"enabled": false, "min_turns_between_knowledge_generation":…` |  |
| `checkpoint-variant` | `claude-1m_840k` |  |
| `context-engine-config` | `{}` |  |
| `context-window-selector` | `false` |  |
| `conversation-memory-sample-interval-minutes` | `5` |  |
| `customization-token-budget` | `20000` |  |
| `deprecate-ephemeral-messages` | `false` |  |
| `disable-system-message-merging` | `false` |  |
| `disable-system-message-tag` | `false` |  |
| `disable-teamwork-forced-flash-model` | `false` | `false` runs Teamwork's orchestrator on Gemini Flash whatever model you picked |
| `enable-compact-slash-command` | `true` |  |
| `enable-context-role` | `false` |  |
| `enable-context-usage-indicator` | `false` |  |
| `enable-continue-after-injection` | `false` |  |
| `enable-gemini-next-opt-out-switch` | `false` |  |
| `enable-model-capacity-exhausted-retries` | `true` |  |
| `enable-orchestrator-model-selection` | `true` |  |
| `enable-plugin-context-providers` | `false` |  |
| `enable-prompt-cache-keep-alive` | `false` |  |
| `enable-reconcile-summaries` | `false` |  |
| `enable-run-workflow-call-tool-prompts` | `false` |  |
| `enable-steer-framing` | `false` |  |
| `enable-subagent-dropdown-models` | `false` |  |
| `enable-thought-editing` | `false` |  |
| `enable-thought-steering-from-selection` | `false` |  |
| `enable-vscode-injection-removal` | `false` |  |
| `enable-work-efficiently-reminder` | `true` |  |
| `full_frame_memory_explorer` | `false` |  |
| `generic-system-prompt` | `true` | Which system-prompt variant the default agent gets |
| `jetski-generative-ui-prompt` | `false` |  |
| `max-best-of-n` | `8` |  |
| `max-tokens-per-step` | `16384` |  |
| `max_injected_memories` | `-1` |  |
| `memory_operations_disabled_surfaces` | `none` |  |
| `memory_source_attribution` | `false` |  |
| `memory_synthesis_mode` | `inline` |  |
| `native-swarm-suggested-models` | `` |  |
| `orchestrator-model-roles` | `{       "orchestrators": [         "gemini-4-neon",         …` |  |
| `remove-conversation-history` | `false` |  |
| `rules-token-budget` | `20000` |  |
| `send-subagent-initial-prompt-as-message` | `true` |  |
| `shadow-call-aicode-models` | `false` |  |
| `show-all-models-in-gemini-app` | `false` |  |
| `show-model-selection-change` | `true` |  |
| `show_memory_operation_diffs` | `false` |  |
| `show_memory_operations` | `true` |  |
| `si_dreaming_split_large_memories` | `false` |  |
| `summary-store-reads` | `false` |  |
| `use-model-reported-token-count` | `true` |  |

## Interface, onboarding & promotion

| Switch | Value | Meaning |
|---|---|---|
| `agy-cli-announcement-message` | `` |  |
| `agy-hub-announcement-message` | `` |  |
| `agy-onboarding-personas` | `` |  |
| `cli-enable-btw-threads` | `false` |  |
| `cli-enable-live-mode` | `false` |  |
| `cli-enable-transcript-mode` | `false` |  |
| `cli-seasonal-theme-periods` | `{"values": ["halloween=2026-10-23..2026-11-01"]}` |  |
| `configure-artifact-width` | `false` |  |
| `enable-authenticated-ui-bootstrap` | `true` |  |
| `enable-aux-pane-redesign` | `false` |  |
| `enable-aux-pane-redesign-extension` | `false` |  |
| `enable-base-ui-tooltips` | `true` |  |
| `enable-cancel-before-send` | `false` |  |
| `enable-context-menu-system` | `false` |  |
| `enable-developer-mode` | `false` |  |
| `enable-enhanced-file-viewer` | `false` |  |
| `enable-feature-manager-keybindings` | `false` |  |
| `enable-file-explorer-tab` | `false` |  |
| `enable-file-viewer-keyboard-navigation` | `false` |  |
| `enable-help-slash-command` | `false` |  |
| `enable-input-autocomplete` | `false` |  |
| `enable-input-draft-persistence` | `false` |  |
| `enable-keybinding-customization` | `false` |  |
| `enable-learn-slash-command` | `true` |  |
| `enable-new-themes` | `false` |  |
| `enable-notifications-settings` | `false` |  |
| `enable-open-with` | `false` |  |
| `enable-owl-slash-command` | `true` | Whether `/boost` is offered |
| `enable-pane-selection-actions` | `false` |  |
| `enable-paste-text-as-snippet` | `false` |  |
| `enable-profiles` | `false` |  |
| `enable-remote-conversation-side-question` | `false` |  |
| `enable-role-onboarding` | `false` |  |
| `enable-run-command-semantic-rendering` | `false` |  |
| `enable-send-immediately-by-default` | `false` |  |
| `enable-settings-error-card` | `true` |  |
| `enable-settings-search` | `false` |  |
| `enable-side-question-rework` | `false` |  |
| `enable-sidebar-inbox-mode` | `false` |  |
| `enable-sidebar-redesign` | `false` |  |
| `enable-simplified-skin` | `false` |  |
| `enable-split-view` | `false` |  |
| `enable-text-transitions` | `false` |  |
| `enable-thumbs-down-reroll` | `false` |  |
| `enable-timeout-notification` | `false` |  |
| `enable-user-message-bubbles` | `false` |  |
| `jetbox-usage-tips` | `{}` |  |
| `jetski-bundle-promotions` | `` |  |
| `jetski-cli-announcement-message` | `` |  |
| `jetski-generative-ui` | `false` |  |
| `jetski-hiring-link-text` | `` |  |
| `jetski-hiring-link-url` | `` |  |
| `jetski-hub-announcement-message` | `Please update Jetski to v2026.10.14.04+. If the Update pill …` |  |
| `markdown-table-v2` | `false` |  |
| `markdown-virtualize-long-documents` | `false` |  |
| `use-slash-plan` | `true` |  |
| `windows-titlebar-redesign` | `false` |  |

## Infrastructure, quotas & other

| Switch | Value | Meaning |
|---|---|---|
| `cli-enable-jetmon` | `false` |  |
| `collect-x20-quotas-on-checkin` | `false` |  |
| `continuous-pprof-cpu-duration-seconds` | `10` |  |
| `continuous-pprof-cpu-interval-minutes` | `90` |  |
| `continuous-pprof-heap-interval-minutes` | `90` |  |
| `continuous-pprof-mem-check-interval-minutes` | `1` |  |
| `continuous-pprof-mem-cooldown-minutes` | `30` |  |
| `continuous-pprof-mem-threshold-gib` | `16` |  |
| `contrib-experiment-variant` | `` |  |
| `cortex-snapshot-recording` | `false` |  |
| `cortex-snapshot-revert` | `false` |  |
| `dark-launch-aicode-quota-status` | `false` |  |
| `dbtrajectory-gen-metadata-cache-ttl-seconds` | `0` |  |
| `dbtrajectory-step-cache-ttl-seconds` | `0` |  |
| `enable-antigravity-api-keys` | `false` |  |
| `enable-background-task-accumulator` | `false` |  |
| `enable-chatski-connect-page` | `false` |  |
| `enable-composer-connectors` | `false` |  |
| `enable-continuous-pprof` | `false` |  |
| `enable-cost-accumulator` | `false` |  |
| `enable-enterprise-remote-ai` | `false` |  |
| `enable-fast-output-close` | `true` |  |
| `enable-file-diff-accumulator` | `true` |  |
| `enable-ge-baic-quota-visibility` | `false` |  |
| `enable-inference-traffic-migration` | `false` |  |
| `enable-jetbox-gcert` | `false` |  |
| `enable-jetbox-gcert-v2` | `false` |  |
| `enable-jetski-chat` | `false` |  |
| `enable-remote-ai` | `false` |  |
| `enable-resource-manager` | `false` |  |
| `enable-sqlite-trajectory` | `true` |  |
| `enable-state-accumulator` | `false` |  |
| `enable-styles-css-cleanup` | `false` |  |
| `enable-wcv-three-layer-stack` | `false` |  |
| `shadow-call-aicode-fetch-licenses` | `false` |  |
| `shadow-call-aicode-mendel` | `true` |  |
| `sign-out-missing-aicode-scope` | `true` |  |
| `use-aicode-fetch-licenses` | `false` |  |
| `use-aicode-jetski-endpoint` | `false` |  |
| `use-aicode-mendel` | `true` |  |
| `use-aicode-preprod-endpoint` | `false` |  |
| `use-canonical-stub-for-checkin` | `false` |  |
| `use-component-rewrite` | `true` |  |
| `use-core-direct` | `true` |  |
| `user-interaction-timeout-seconds` | `0` |  |
| `verbose-errors` | `false` |  |

## Per-model switches

`fetchAvailableModels` also sends switches for each model. Full response: [available-models.json](available-models.json).

| Switch | Models that have it | Meaning |
|---|---|---|
| `CASCADE_USE_EXPERIMENT_CHECKPOINTER` | 35 | Compaction strategy, window limit and threshold for this model |
| `CASCADE_USE_EXPERIMENT_CHECKPOINTER/claude-1m_840k` | 6 |  |
| `allow-experimental-monitors` | 17 | Turns on Google DeepMind's review models for this model |
| `cascade-include-ephemeral-message` | 21 |  |
| `plan-execute-config` | 2 |  |
| `retry_model_capacity_exhausted` | 33 |  |
| `task-details-suffix` | 16 |  |
| `template__system_prompts__guidelines` | 7 | Replaces the prompt's `<guidelines>` section for this model |
