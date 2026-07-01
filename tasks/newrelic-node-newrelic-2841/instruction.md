Implement a health reporting feature for the Node.js agent to integrate with centralized management systems in containerized environments. Ensure the agent periodically writes a structured status file to a specified directory, reflecting its health status and any issues encountered.

*   Create the `HealthReporter` class in `lib/health-reporter.js`.
    *   Accept an optional object with `agentConfig`, `logger`, and `setInterval` as constructor arguments.
    *   Export `HealthReporter` as the module's default export.
    *   When called with no arguments, the reporter should be disabled.

*   Implement health reporting behavior:
    *   Log 'new relic agent control disabled, skipping health reporting' if `agent_control.enabled` is not true.
    *   Log 'health check output directory not accessible, skipping health reporting' if the output directory is inaccessible.
    *   Generate a health status file path in the format `health-{UUID}.yaml` and return it via the `destFile` getter.
    *   Write the status file using `fs.writeFile` with the specified content format and encoding.
    *   Schedule periodic health checks using `setInterval` at intervals specified by `agent_control.health.frequency`.
    *   Handle file writing errors by logging 'error when writing out health status: {error.message}'.

*   Define static constants for status codes and messages:
    *   Include constants like `STATUS_HEALTHY`, `STATUS_INVALID_LICENSE_KEY`, etc., with corresponding messages.

*   Implement the `setStatus` method:
    *   Do nothing if the reporter is disabled.
    *   Log 'invalid health reporter status provided: {status}' for unrecognized status codes.
    *   Update the current status appropriately.

*   Implement the `stop` method:
    *   Immediately call `done()` if the reporter is disabled.
    *   Clear the periodic interval and write a final status file.
    *   Log errors during the final write and call `done()` after completion.

*   Support configuration via environment variables:
    *   Map environment variables like `NEW_RELIC_AGENT_CONTROL_ENABLED` to configuration settings.
    *   Convert file URIs to filesystem paths using `fileURLToPath`.

*   Ensure integration with the agent:
    *   Expose a `healthReporter` property in the `Agent` class.
    *   Call `setStatus` with appropriate codes in various scenarios like startup, shutdown, and error conditions.

*   Update the application entry point and collector API to set health statuses based on application and connection states.

*   Extend the match custom assertion utility to support array-typed expected values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.