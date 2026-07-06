## Description

The Java code generator for the Matter/CHIP SDK currently produces cluster management code that uses a modern Java resource cleanup API to handle native memory deallocation when objects are garbage collected. However, this API is not available or compatible with the older Android environments that the CHIP SDK targets, making the generated code unusable in those environments.

## Expected Behavior

- The code generator's Java output should not rely on the modern Java cleanup API for managing native resource lifecycle.
- Instead, the generated Java cluster base class should use the traditional object finalization mechanism to trigger native resource cleanup when an object is garbage collected.
- The finalizer in the generated code must safely release the associated native cluster pointer if it has not already been released.
- The generator template itself must be updated so that all future code generation also produces the updated pattern.

## Why This Matters

Without this fix, the generated Java files fail to compile or run in older Android environments. Any project using the CHIP SDK's generated Java bindings would be blocked from targeting those environments. Switching to the older, widely supported finalization approach restores broad platform compatibility while still ensuring native resources are cleaned up automatically when cluster objects become unreachable.
