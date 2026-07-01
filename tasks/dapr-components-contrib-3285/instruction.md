Implement a table access validation check in the Init method of the DynamoDB state store. Ensure that the initialization process verifies the existence and accessibility of the configured table, and handle any errors appropriately. Respect any pre-configured clients injected before initialization.

Requirements:

*   Modify the Init method of the DynamoDB state store to include a table access validation check.
    *   Issue a read request against the configured table using the existing client.
*   Preserve any pre-configured client:
    *   If the client field on the StateStore is non-nil before Init is called, use this client for the validation check.
    *   Do not replace the pre-configured client with a new one from the internal client factory.
*   Handle table access validation errors:
    *   If the validation call fails, return an error from Init.
    *   Format the error message as: "error validating DynamoDB table '<table_name>' access: <underlying_error>".
        *   Replace `<table_name>` with the actual table name from the metadata configuration.
        *   Replace `<underlying_error>` with the error message from the client.
    *   Specifically, if the table name is 'does-not-exist' and the client returns 'Requested resource not found', return: "error validating DynamoDB table 'does-not-exist' access: Requested resource not found".
*   If the table access validation succeeds without errors, complete the Init method without returning an error.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.