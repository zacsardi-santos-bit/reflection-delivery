Implement a feature in the OpenTelemetry input plugin for Fluent Bit to preserve the hierarchical context of OTLP log payloads. Ensure that resource and scope metadata are included in the output, and modify the library output plugin to support a new delivery mode for complete raw event chunks.

*   Update the OpenTelemetry input plugin to:
    *   Process OTLP logs received via HTTP POST to the /v1/logs endpoint.
    *   Package each resource-scope combination in the OTLP payload as a group within the output event chunk.
    *   Include a group header record with metadata and body fields:
        *   Metadata must be a msgpack map with keys: 'schema' (value 'otlp'), 'resource_id', and 'scope_id'.
        *   Body must contain 'resource' and 'scope' maps with attributes converted to key-value maps.
    *   Ensure individual log records follow the group header, preserving the log record body.
    *   Return HTTP status 201 for successful log ingestion, supporting alternative response codes via the 'successful_response_code' option.
    *   Ensure logs are routed correctly when 'tag_from_uri' is set to false.

*   Modify the out_lib output plugin to:
    *   Support a new configuration property 'data_mode' with values 'single_record' (default) and 'chunk'.
    *   Pass the entire raw event chunk to the callback function when 'data_mode' is set to 'chunk'.
    *   Define constants FLB_DATA_MODE_SINGLE_RECORD (0) and FLB_DATA_MODE_CHUNK (1) in plugins/out_lib/out_lib.h.
    *   Extend struct flb_out_lib_config with 'data_mode' and 'data_mode_str' fields.

*   Modify function signatures and behaviors:
    *   Update out_lib_flush to check ctx->data_mode and handle chunk delivery mode.
    *   Extend process_json_payload_resource_logs_entry with a resource_logs_index parameter and iterate over scope_logs entries.
    *   Update process_json_payload_root to pass the loop index as the resource_logs_index argument.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.