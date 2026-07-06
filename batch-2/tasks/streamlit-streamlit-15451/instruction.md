I'm working on Streamlit's internal runtime and I'd like to refactor how per-run mutable state is organized in the script run context.

*   A new module must exist at `streamlit/runtime/scriptrunner_utils/shared_run_state.py` that exports the `SharedRunState` class, importable as `from streamlit.runtime.scriptrunner_utils.shared_run_state import SharedRunState`.

*   The `SharedRunState` class must have four `ThreadSafeSet` attributes: `widget_ids_this_run`, `widget_user_keys_this_run`, `form_ids_this_run`, and `new_fragment_ids`, each initialized as a fresh empty `ThreadSafeSet` on construction.

*   The `SharedRunState.tracked_commands` property must return an immutable tuple (not a list); any attempt to call `.append()` on the returned object must raise an `AttributeError`.

*   The `SharedRunState.tracked_commands_count` must be an integer equal to the number of commands currently stored in the tracked list (not exceeding the per-command cap).

*   The `SharedRunState.track_command(command, max_per_command)` method must append the command to the tracked list only if that command name has been tracked fewer than `max_per_command` times so far; it must always increment the raw per-name counter regardless of the cap. Concurrent calls from multiple threads must not lose any commands.

*   The `SharedRunState.command_count_for(name)` method must return the raw uncapped count of how many times a command with the given name has been tracked, even after the per-command cap has stopped adding the command to the list.

*   The `SharedRunState.reset()` method must clear all four `ThreadSafeSet` fields in place and reset telemetry state: `tracked_commands` must return `()`, `tracked_commands_count` must be `0`, and `command_count_for(name)` must return `0` for any previously tracked command name.

*   Each newly constructed `ScriptRunContext` instance must have a `shared` attribute that is a `SharedRunState` instance. Two independently constructed `ScriptRunContext` instances must each own a distinct `SharedRunState` object (they must not share the same instance).

*   Calling `ctx.reset()` on a `ScriptRunContext` must reset `ctx.shared`, clearing all widget/form/fragment sets and resetting `tracked_commands` to `()` and `tracked_commands_count` to `0`.

*   When telemetry collection encounters an error, `ctx.shared.tracked_commands` must equal `()` (an empty tuple, not an empty list).


*   Interface details: Type: Class
Name: SharedRunState
Location: lib/streamlit/runtime/scriptrunner_utils/shared_run_state.py
Description: Container for the mutable per-run state shared across threads within a single script run. Holds widget/form/fragment tracking sets and command telemetry state.
Attributes:
  - widget_ids_this_run: ThreadSafeSet — tracks widget IDs registered this run
  - widget_user_keys_this_run: ThreadSafeSet — tracks user-supplied widget keys this run
  - form_ids_this_run: ThreadSafeSet — tracks form IDs registered this run
  - new_fragment_ids: ThreadSafeSet — tracks new fragment IDs registered this run
  - tracked_commands_count: int — count of commands currently stored in the tracked list (capped by per-command maximum)
Properties:
  - tracked_commands: tuple — immutable snapshot of tracked Command objects; returns () when empty; raises AttributeError if caller attempts to call .append() on it
Methods:
  - track_command(command: Command, max_per_command: int) -> None: Appends command to the tracked list only if the per-command cap has not been reached; always increments the raw per-name counter regardless of cap. Thread-safe: concurrent calls must not lose any commands.
  - command_count_for(name: str) -> int: Returns the raw (uncapped) count of how many times a command with the given name has been tracked.
  - reset() -> None: Clears all ThreadSafeSet fields and resets telemetry state (tracked_commands becomes (), tracked_commands_count becomes 0, all command counts become 0).

Type: Attribute
Name: shared
Location: lib/streamlit/runtime/scriptrunner_utils/script_run_context.py (on ScriptRunContext)
Description: Each ScriptRunContext instance must own an independent SharedRunState instance as its `shared` attribute. Instances must not share the same SharedRunState object. ScriptRunContext.reset() must also call reset() on the shared state.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.