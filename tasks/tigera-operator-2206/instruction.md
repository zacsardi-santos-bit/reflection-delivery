Update the Dex authentication service container's security configuration to enhance its security posture. Ensure the container drops all Linux capabilities and applies the runtime default process isolation profile, while maintaining existing non-root user settings.

*   Configure the Dex container's security context to:
    *   Drop all Linux capabilities by setting `Capabilities.Drop` to include "ALL".
    *   Apply the runtime default process isolation profile by setting `SeccompProfile.Type` to `RuntimeDefault`.
*   Ensure these new security settings coexist with the current security context:
    *   `RunAsGroup` is set to 1001.
    *   `RunAsNonRoot` is set to true.
    *   `RunAsUser` is set to 1001.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.