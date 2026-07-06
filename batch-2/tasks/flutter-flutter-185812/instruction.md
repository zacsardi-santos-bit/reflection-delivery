I'm running into a crash in the Flutter tool's interactive terminal mode.

*   When the platform toggle terminal input ('o' or 'O') is processed and the VM service is null, the handler must not crash or throw an exception.

*   When the platform toggle terminal input ('o' or 'O') is processed and the VM service is null, the status logger output must contain the message 'Platform toggle is not supported for this device.'

*   The behavior for 'o' and 'O' keys must be consistent — both keys must produce the same graceful null-vmService handling.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.