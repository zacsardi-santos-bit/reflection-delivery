I'm working on Prefect's flow engine and need to add support for running flows from a command-line entrypoint — the kind of thing a worker does when it launches a flow run in a subprocess.

*   The `_load_flow_from_runtime_entrypoint` function must accept an entrypoint string in the format `filepath:function_name` (e.g., `/path/to/file.py:dog`) and return a Prefect Flow object whose `name` matches the function name and whose `fn` is callable.

*   The `_load_flow_from_runtime_entrypoint` function must also accept module path format entrypoints (e.g., `package.submodule:function_name`) and correctly load the flow from that module when the working directory is appropriate.

*   When the loaded object is a plain Python function (not already a Prefect Flow), `_load_flow_from_runtime_entrypoint` must automatically convert it into a Flow object rather than raising an error.

*   The `_main` function must accept a list of command-line arguments and return the integer `1` if the list does not contain exactly one element (zero or two or more arguments are all invalid).

*   The `_main` function must read the `PREFECT__FLOW_RUN_ID` environment variable and return `1` if it is absent or if its value is not a valid UUID.

*   When given exactly one argument and a valid UUID in `PREFECT__FLOW_RUN_ID`, `_main` must call `_run_flow_from_runtime_entrypoint` with the parsed UUID and the entrypoint string, then return `0`.

*   The `_run_flow_from_runtime_entrypoint` function must accept a UUID `flow_run_id` and an entrypoint string. It must call `configure_from_env`, then load the flow run via `load_flow_run`, then load the flow via `_load_flow_from_runtime_entrypoint`, then obtain a logger via `flow_run_logger`.

*   Inside `_run_flow_from_runtime_entrypoint`, `RunMetrics` must be used as a context manager initialized with `(flow_run, flow)`. Within that context, `run_flow` must be called and its result passed to `_drive_run_flow_result`. The execution order must be: configure → RunMetrics context enter → _drive_run_flow_result → RunMetrics context exit.


*   Interface details: Type: Function
Name: _load_flow_from_runtime_entrypoint
Location: src/prefect/flow_engine.py
Signature: _load_flow_from_runtime_entrypoint(entrypoint: str) -> Flow
Description: Loads a flow from an entrypoint string in the format "filepath:function_name" or "module.path:function_name". If the loaded object is a plain Python function (not already a Prefect Flow), it must be automatically converted into a Flow. Returns the Flow object with its name set to the function name.

Type: Function
Name: _main
Location: src/prefect/flow_engine.py
Signature: _main(argv: list) -> int
Description: Entry point for running a flow from the command line. Takes a list of argv strings (not including the program name). Returns 1 if argv does not contain exactly one element (the entrypoint), or if the PREFECT__FLOW_RUN_ID environment variable is absent or not a valid UUID. On success, calls _run_flow_from_runtime_entrypoint with the parsed UUID and the entrypoint, then returns 0.

Type: Function
Name: _run_flow_from_runtime_entrypoint
Location: src/prefect/flow_engine.py
Signature: _run_flow_from_runtime_entrypoint(flow_run_id: UUID, entrypoint: str) -> None
Description: Orchestrates a complete flow run from a runtime entrypoint. Must execute in this order: (1) call configure_from_env(), (2) call load_flow_run(flow_run_id) to get the flow run, (3) call _load_flow_from_runtime_entrypoint(entrypoint) to load the flow, (4) call flow_run_logger(flow_run) to get a logger, (5) enter RunMetrics(flow_run, flow) as a context manager, (6) call run_flow(flow, flow_run, error_logger) to get a result, (7) call _drive_run_flow_result(flow, run_result), (8) exit the RunMetrics context.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.