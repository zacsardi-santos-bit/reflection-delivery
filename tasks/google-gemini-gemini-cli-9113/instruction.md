Implement the ability to configure telemetry settings in the Gemini CLI using environment variables, ensuring they take precedence over settings file values. Prioritize command-line arguments over environment variables, and provide clear error messages for invalid environment variable values.

*   Implement `parseBooleanEnvFlag` in `packages/core/src/telemetry/config.ts`:
    *   Return `undefined` for `undefined` input.
    *   Return `true` only for 'true' or '1' (case-sensitive).
    *   Return `false` for all other strings, including 'false', '0', 'TRUE', 'random', and ''.

*   Implement `parseTelemetryTargetValue` in `packages/core/src/telemetry/config.ts`:
    *   Return `TelemetryTarget.LOCAL` for 'local' or `TelemetryTarget.LOCAL`.
    *   Return `TelemetryTarget.GCP` for 'gcp' or `TelemetryTarget.GCP`.
    *   Return `undefined` for unrecognized values or `undefined` input.

*   Implement `resolveTelemetrySettings` in `packages/core/src/telemetry/config.ts`:
    *   Accept an options object with optional `argv`, `env`, and `settings` fields.
    *   Apply priority: `argv` > `env` (GEMINI_* variables) > `settings`.
    *   Support `GEMINI_TELEMETRY_ENABLED`, `GEMINI_TELEMETRY_TARGET`, `GEMINI_TELEMETRY_OTLP_ENDPOINT`, `GEMINI_TELEMETRY_OTLP_PROTOCOL`, `GEMINI_TELEMETRY_LOG_PROMPTS`, `GEMINI_TELEMETRY_OUTFILE`, and `GEMINI_TELEMETRY_USE_COLLECTOR`.
    *   Use `OTEL_EXPORTER_OTLP_ENDPOINT` as a fallback for `otlpEndpoint`.
    *   Throw an error for unrecognized protocol values with a message matching `/Invalid telemetry OTLP protocol/i`.
    *   Throw an error for unrecognized target values with a message matching `/Invalid telemetry target/i`.
    *   Return a `TelemetrySettings` object with fields: `enabled`, `target`, `otlpEndpoint`, `otlpProtocol`, `logPrompts`, `outfile`, `useCollector`.

*   Implement `loadCliConfig` in `packages/cli/src/config/config.ts`:
    *   Resolve telemetry settings from environment variables with priority over settings file values.
    *   Throw an error for invalid telemetry target with a message matching `/Invalid telemetry configuration: .*Invalid telemetry target/i`.
    *   Ensure the returned `Config` object has getter methods: `getTelemetryEnabled()`, `getTelemetryTarget()`, `getTelemetryOtlpEndpoint()`, `getTelemetryOtlpProtocol()`, `getTelemetryLogPromptsEnabled()`, `getTelemetryOutfile()`, `getTelemetryUseCollector()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.