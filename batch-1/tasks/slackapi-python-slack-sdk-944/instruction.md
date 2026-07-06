Implement enhancements to the Slack SDK's model classes to preserve unrecognized API response fields in a dedicated attribute. This will allow developers to access new or unexpected API data without waiting for an SDK update.

*   Update `LogsResponse` in `slack_sdk/audit_logs/v1/logs.py`:
    *   Add an `unknown_fields` attribute of type `Dict[str, Any]`.
    *   Ensure the constructor accepts `**kwargs` and assigns unrecognized fields to `self.unknown_fields`.

*   Update `Entry` in `slack_sdk/audit_logs/v1/logs.py`:
    *   Add an `unknown_fields` attribute of type `Dict[str, Any]`.
    *   Ensure the constructor accepts `**kwargs` and assigns unrecognized fields to `self.unknown_fields`.

*   Update `ResponseMetadata` in `slack_sdk/audit_logs/v1/logs.py`:
    *   Add an `unknown_fields` attribute of type `Dict[str, Any]`.
    *   Ensure the constructor accepts `**kwargs` and assigns unrecognized fields to `self.unknown_fields`.

*   Update `User` in `slack_sdk/scim/v1/user.py`:
    *   Rename the existing `_additional_fields` or `additional_fields` to `unknown_fields`.
    *   Ensure `unknown_fields` captures unrecognized API fields in snake_case format.
    *   Update the `to_dict()` method to include `unknown_fields` with keys converted back to camelCase.

*   Update `_to_dict_without_not_given` in `slack_sdk/scim/v1/internal_utils.py`:
    *   Check for the `unknown_fields` key on objects.
    *   Merge `unknown_fields` into the output dictionary with keys converted to camelCase.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.