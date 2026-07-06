Refactor the `_get_result` function in `salt/states/module.py` to correctly determine the success or failure of a module function call from a single return value. Update all call sites to use the new single-argument form.

*   Modify the `_get_result` function to accept a single positional argument `func_ret`, which represents the full return value from a called function.
*   Implement logic to handle the following scenarios:
    *   If `func_ret` is a boolean, return it directly.
    *   If `func_ret` is a dictionary:
        *   Return False if the dictionary contains a 'result' key set to False.
        *   Return False if the dictionary contains a 'retcode' key with a non-zero value, including string representations like '-1'.
        *   If both 'retcode' and 'result' keys are present, return False if 'retcode' is non-zero, regardless of the 'result' value.
        *   If the dictionary contains a 'changes' key with a nested dictionary, inspect it for 'result' and 'retcode' keys using the same rules:
            *   Return False if 'result' is False or if 'retcode' is non-zero.
            *   Return True only if both 'result' is True and 'retcode' is 0.
*   Update all calls to `_get_result` within `salt/states/module.py` to pass only the single `func_ret` argument.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.