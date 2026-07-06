I'm seeing a bug in the chat widget's message submission logic.

*   When a session is configured with both a permission profile and the external sandbox policy variant, and the user submits a message, the resulting user turn operation must include the permission profile that was set in the session configuration — it must not be dropped or replaced with a null value.

*   The permission profile must be preserved in the submitted user turn regardless of which sandbox policy variant is active, including the legacy external sandbox variant.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.