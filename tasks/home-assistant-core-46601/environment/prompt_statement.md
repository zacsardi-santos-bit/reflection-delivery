I'm working on the Z-Wave JS integration for Home Assistant and I'd like to add WebSocket API support for managing the Z-Wave JS driver's logging configuration. Right now there's no way to view or change the driver's log settings through the WebSocket API at all.

I need two new WebSocket commands: one to retrieve the current log configuration and one to update it. The update command should let callers change any combination of settings — whether logging is enabled, the verbosity level, whether to write logs to a file, the log file path, and whether to force console output. The get command should return all of these settings in the response.

The update command needs solid input validation. It should reject requests that don't provide at least one setting to change, reject unrecognized verbosity level names, and reject attempts to enable file logging without also providing a log file path — each with a clear error message. The verbosity level input should be accepted as a human-readable string and converted appropriately before being sent to the driver.

The response from the get command should include the log level as a numeric value matching the driver's enum representation, and all field names in the response should use snake case.
