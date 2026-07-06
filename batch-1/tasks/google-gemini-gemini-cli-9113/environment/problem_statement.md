## Description

Telemetry settings in Gemini CLI can currently only be configured through a settings file. This is limiting for users who work in automated environments like CI/CD pipelines, Docker containers, or shared machines where modifying a settings file for every configuration variation is impractical or undesirable.

## Expected Behavior

- All telemetry settings (whether telemetry is enabled, what target backend to send data to, the endpoint and protocol for export, whether to log prompts, the output file path, and whether to use a collector) should be overridable using environment variables at runtime.
- Environment variables must take priority over settings file values when both are present.
- Command-line arguments must take priority over environment variables when both are present.
- When an environment variable is set to an invalid value (such as an unrecognized target backend name), the system must immediately report a clear error rather than silently ignoring it or using an unexpected default.
- When no environment variable is set, the system must fall back to the value from the settings file.
- For the OTLP endpoint, there should be a fallback to a standard OpenTelemetry environment variable when the Gemini-specific one is not set.

## Why This Matters

This makes it possible to use Gemini CLI in automated and containerized environments where runtime configuration through environment variables is the standard approach, without needing to modify a settings file. It also makes the configuration more auditable and consistent with common tooling conventions.
