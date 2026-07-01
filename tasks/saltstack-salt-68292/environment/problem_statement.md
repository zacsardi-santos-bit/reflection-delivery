# minion.restart doesn't work on systemd or Windows service-managed systems

## Description

The minion restart execution module function fails to correctly restart a salt minion on systems that use a service management framework (such as modern Linux init systems or Windows services). Currently the function relies on a direct process-kill approach regardless of the host platform, which does not work with managed services and can leave the minion in a stopped state.

There is also no way to schedule a recovery retry: if the initial restart attempt fails or the service doesn't come back on its own, there is no built-in mechanism to queue a delayed start attempt.

## Expected Behavior

- The restart function should detect whether the system uses a recognized service manager and, if so, delegate the restart to that service manager rather than killing the process directly.
- On systems without a recognized service manager, the existing process-kill fallback should continue to work.
- A new optional parameter should allow callers to schedule a retry job that will attempt to start the minion again after a configurable number of seconds, in case the primary restart doesn't bring the minion back.
- If scheduling the retry fails, the error should be surfaced clearly and the primary restart should not proceed.
- If the primary restart fails after the retry was successfully scheduled, both pieces of information should appear in the result.
- The retry scheduling option should be ignored on systems that don't use a recognized service manager.

## Why This Matters

Without this fix, operators on systemd-based Linux systems and Windows cannot reliably restart a salt minion through the restart function. The scheduled-retry feature adds operational resilience for environments where transient failures during restart are possible.
