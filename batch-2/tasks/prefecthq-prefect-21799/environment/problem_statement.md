## Description

Prefect needs a standardized entry point for running flows launched by a worker or agent in a separate subprocess or container. Currently, there is no built-in way for the flow engine to load a flow from a file path or module reference, read the flow run ID from the environment, and orchestrate the full execution lifecycle. This means each worker implementation must handle this wiring manually or rely on ad-hoc conventions.

## Expected Behavior

- The flow engine should expose a function that loads a flow given an entrypoint string (either a file path with a function name, or a Python module path with a function name).
- If the referenced function is a plain Python function rather than a decorated flow, it should automatically be wrapped as a flow rather than failing.
- There should be a main entry point function that reads the flow run ID from a designated environment variable and a single entrypoint argument. It should return a non-zero exit code when the argument count is wrong or when the environment variable is missing or does not contain a valid identifier.
- There should be a function that ties the pieces together: configuring from the environment, loading the flow run and the flow, setting up logging and metrics, running the flow, and driving the result — all in the correct order.

## Why This Matters

Workers that launch flow runs in subprocesses need a reliable, consistent mechanism for invoking flows. Having these utilities built into the flow engine makes it easier to implement and test subprocess-based execution without duplicating orchestration logic.
