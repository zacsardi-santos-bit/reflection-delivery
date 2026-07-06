## Description

The SDK's setup wizard allows users to configure their API key, workspace, and server URL, but does not let them specify which project traces should be sent to as part of that process. The success message shown at the end of setup references a project name from the stored configuration rather than any project the user actually chose during setup — and there is no way to choose one.

Users who want to direct their traces to a specific project currently have to do so after configuration, through a separate step. This means the configuration tool does not cover all four of the essential connection parameters it should.

## Expected Behavior

- When setting up the SDK, users should be able to specify a project name alongside the API key, workspace, and URL.
- If no project name is given, the setup wizard should prompt the user (or fall back to a default silently when running in non-interactive or auto-approve mode).
- An existing project name in the configuration file should be reused when not explicitly overridden.
- If the user is prompted and provides an empty project name, the setup should fail with a clear error.
- The chosen project name should be saved to the configuration file, reflected in the session, and set in the environment so that downstream integrations can read it.
- The completion message should display the actual project name that was selected, not one pulled from the pre-existing config.

## Why This Matters

Without this, users have no way to lock in a target project during initial setup. Third-party integrations that rely on the configured environment to determine where to send traces cannot pick up the project name from the environment after running the setup wizard. Making project name a first-class part of the configuration process brings it in line with the other three core settings and reduces the need for post-setup manual steps.
