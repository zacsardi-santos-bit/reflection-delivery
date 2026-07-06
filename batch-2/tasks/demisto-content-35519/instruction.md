Implement content type support for reference list commands and add a new command to verify list content before committing it. Ensure that the create, update, and verify commands validate both the 'lines' and 'content_type' arguments correctly.

Requirements:

*   Export a constant:
    *   Name: `VALID_CONTENT_TYPE`
    *   Location: `Packs/GoogleChronicleBackstory/Integrations/GoogleChronicleBackstory/GoogleChronicleBackstory.py`
    *   Value: `['PLAIN_TEXT', 'CIDR', 'REGEX']`

*   Update `gcb_create_reference_list_command`:
    *   Location: `Packs/GoogleChronicleBackstory/Integrations/GoogleChronicleBackstory/GoogleChronicleBackstory.py`
    *   Signature: `gcb_create_reference_list_command(client_obj, args) -> Tuple[str, dict, dict]`
    *   Validate 'lines' argument:
        *   Split by delimiter, filter empty strings.
        *   Raise `ValueError` with 'Missing argument lines.' if empty.
    *   Validate 'content_type' against `VALID_CONTENT_TYPE`:
        *   Raise `ValueError` with message from `MESSAGES['VALIDATE_SINGLE_SELECT']`.
    *   Handle API 400 response:
        *   Raise `ValueError` with 'Status code: 400\nError: <error_message>'.

*   Update `gcb_update_reference_list_command`:
    *   Location: `Packs/GoogleChronicleBackstory/Integrations/GoogleChronicleBackstory/GoogleChronicleBackstory.py`
    *   Signature: `gcb_update_reference_list_command(client_obj, args) -> Tuple[str, dict, dict]`
    *   Validate 'lines' argument:
        *   Split by delimiter, filter empty strings.
        *   Raise `ValueError` with 'Missing argument lines.' if empty.
    *   Validate 'content_type' against `VALID_CONTENT_TYPE`:
        *   Raise `ValueError` with message from `MESSAGES['VALIDATE_SINGLE_SELECT']`.
        *   If not provided, fetch existing list for current `content_type`.
    *   Handle API 400 response:
        *   Raise `ValueError` with 'Status code: 400\nError: <error_message>'.

*   Implement `gcb_verify_reference_list_command`:
    *   Location: `Packs/GoogleChronicleBackstory/Integrations/GoogleChronicleBackstory/GoogleChronicleBackstory.py`
    *   Signature: `gcb_verify_reference_list_command(client_obj, args) -> Tuple[str, dict, dict]`
    *   Validate 'lines' argument:
        *   Split by delimiter, filter empty strings.
        *   Raise `ValueError` with 'Missing argument lines.' if empty.
    *   Validate 'content_type' against `VALID_CONTENT_TYPE`:
        *   Raise `ValueError` with message from `MESSAGES['VALIDATE_SINGLE_SELECT']`.
    *   On API success (200):
        *   Return `(human_readable, entry_context, raw_json)`.
        *   `entry_context` path: 'GoogleChronicleBackstory.VerifyReferenceList(val.command_name == obj.command_name)'.
        *   `raw_json` includes 'command_name', 'success', and 'errors'.
    *   Human-readable output:
        *   Success: "### All provided lines meet validation criteria."
        *   Failure: Markdown table with 'Line Number' and 'Message'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.