Update the Trend Micro Vision One V3 integration to align with the new API client library version. Implement new commands for managing custom scripts and ensure all existing commands use the updated namespaced client methods and response model classes.

*   Implement the `run_custom_script` function:
    *   Accept a `block_objects` argument (JSON list of objects with 'filename', 'endpoint', optional 'parameter', optional 'description').
    *   Call `client.script.run()` with the script tasks.
    *   Return a `CommandResults` with `outputs_prefix='VisionOne.Run_Custom_Script'`, `outputs_key_field='task_id'`, and outputs where each item has a 'status' field (integer, 202 for accepted) and a 'task_id' field (string).

*   Implement the `get_custom_script_list` function:
    *   Accept optional `filename`, `filetype`, and `query_op` arguments.
    *   Call `client.script.list()` with appropriate filter fields.
    *   Return a `CommandResults` with `outputs_prefix='VisionOne.Get_Custom_Script_List'`, `outputs_key_field='id'`, and outputs where each item has 'id' (str), 'filename' (str), and 'filetype' (str).

*   Implement the `download_custom_script` function:
    *   Accept a `script_id` argument.
    *   Call `client.script.download()`.
    *   Return a `CommandResults` with `outputs_prefix='VisionOne.Download_Custom_Script'`, `outputs_key_field='text'`, and outputs dict containing 'text' (str with the script content).

*   Implement the `delete_custom_script` function:
    *   Accept a `script_id` argument.
    *   Call `client.script.delete()`.
    *   Return a `CommandResults` with `outputs_prefix='VisionOne.Delete_Custom_Script'`, `outputs_key_field='status'`, and outputs dict containing 'status' as a string.

*   Implement the `add_custom_script` function:
    *   Accept `file_url`, `filename`, `filetype`, `description` arguments.
    *   Call `client.script.create()`.
    *   Return a `CommandResults` with `outputs_prefix='VisionOne.Add_Custom_Script'`, `outputs_key_field='id'`, and outputs dict containing 'id' (str, the created script's ID).

*   Implement the `update_custom_script` function:
    *   Accept `filetype`, `filename`, `script_id`, `file_url`, `description` arguments.
    *   Call `client.script.update()`.
    *   Return a `CommandResults` with `outputs_prefix='VisionOne.Update_Custom_Script'`, `outputs_key_field='status'`, and outputs dict containing 'status' (str, value of result code).

*   Update existing handler functions to use the new namespaced client API structure:
    *   Account operations: `client.account.*`
    *   Object operations: `client.object.*`
    *   Email operations: `client.email.*`
    *   Endpoint operations: `client.endpoint.*`
    *   Sandbox operations: `client.sandbox.*`
    *   Task operations: `client.task.*`
    *   Alert operations: `client.alert.*`
    *   Note operations: `client.note.create`

*   Update the integration to use renamed pytmv1 response classes:
    *   Use `GetAlertResp`, `ListEmailActivityResp`, `ListEndpointActivityResp`, `GetEmailActivitiesCountResp`, `ListSandboxSuspiciousResp`.
    *   Construct `SandboxSuspiciousObject` using 'value' field and include a 'type' field of `ObjectType`.
    *   Import `Account`, `Iam`, and `Script` from `pytmv1.model.common`.

*   Ensure `AddAlertNoteResp` is constructed using a 'note_id' field (str).

*   Update the `get_endpoint_info` function to parse the 'endpoint' argument as a JSON-encoded dict and pass its key-value pairs as keyword arguments to `client.endpoint.consume_data`.

*   Create test data files in the `test_data/` directory:
    *   `run_custom_script.json`: JSON array with "status" (integer 202) and "headers" (list) fields.
    *   `add_custom_script.json`: JSON object with "script_id" field.
    *   `update_custom_script.json`: JSON object resulting in a "status" output of "SUCCESS".
    *   `delete_custom_script.json`: JSON object with a "status" field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.