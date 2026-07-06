## Description

The bundler's macro system has several bugs related to how it handles failures that occur when loading a macro module (as opposed to failures that occur during macro execution).

Currently, when a macro module cannot be found or contains a syntax error that prevents it from being loaded, the error message incorrectly says the failure happened during "evaluation" rather than during "loading". This makes it harder to understand the root cause of the problem.

Additionally, the error is pointed at the wrong location in the source code — it highlights the call site where the macro is invoked, rather than the import declaration where the macro is defined. This makes it harder for developers to know where the problem originates.

A third bug: if the same broken macro is called multiple times in the same file, the bundler emits a separate error for each call, flooding the user with duplicate diagnostics when a single error would suffice.

Finally, watch mode does not recover correctly when a macro file has a load error. After fixing the broken macro file, the bundler fails to trigger a successful rebuild.

## Expected Behavior

- When a macro cannot be loaded, the error message should clearly say the failure occurred during loading (not evaluation).
- The error location should point to the import declaration, not the call site.
- When the same macro fails to load and is called multiple times, only one error should be reported.
- In watch mode, fixing a broken macro file should trigger a successful rebuild that correctly executes the macro and produces output.

## Why This Matters

These bugs make macro error messages confusing and noisy, and prevent developers from recovering from macro errors in watch mode without restarting the bundler.
