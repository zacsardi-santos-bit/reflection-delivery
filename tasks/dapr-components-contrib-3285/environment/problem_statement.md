## Description

The DynamoDB state store component does not validate that the configured table exists and is accessible when it first starts up. It only checks that the configuration metadata is syntactically valid. As a result, a misconfigured component — pointing at a non-existent table, a wrong region, or using credentials without the necessary permissions — will initialize successfully and only fail when the first actual read or write operation is attempted.

## Expected Behavior

- When the state store initializes, it should attempt to contact the configured DynamoDB table and verify that access is possible.
- If the table does not exist, is in the wrong region, or the credentials lack the required permissions, initialization should fail immediately with a clear error message identifying the inaccessible table.
- If a client is already available (e.g., pre-configured before initialization), it should be used for this validation check rather than being replaced.

## Why This Matters

Detecting misconfiguration at startup is far better than discovering it at runtime. Without an upfront check, a DynamoDB state store that is silently pointing at the wrong table or using expired credentials will appear healthy until a workload actually tries to use it, at which point errors surface in production. Failing fast with a descriptive error at initialization time makes the system easier to operate and debug.
