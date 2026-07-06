I'm working with the conversation simulator in MLflow's GenAI module and I've noticed it attaches simulation metadata to traces by wrapping the prediction function in a tracing decorator.

*   The simulator must use mlflow.tracing.context as a context manager (with session_id and metadata parameters) to attach simulation information to traces generated during each turn, replacing the previous approach of wrapping the prediction function in a tracing decorator.

*   mlflow.tracing.context must be called exactly once per simulation turn (e.g., 2 calls for a 2-turn simulation).

*   The metadata dict passed to mlflow.tracing.context must include 'mlflow.simulation.goal' (the goal text truncated to _MAX_METADATA_LENGTH characters) and 'mlflow.simulation.persona' (the persona or DEFAULT_PERSONA, truncated to _MAX_METADATA_LENGTH characters).

*   The metadata dict passed to mlflow.tracing.context must include 'mlflow.simulation.turn' as a string representation of the turn number.

*   When simulation_guidelines are provided, the metadata dict must include 'mlflow.simulation.simulation_guidelines' as a single string (joining list items with newlines if a list), truncated to _MAX_METADATA_LENGTH characters.

*   The trace session ID must be passed as the session_id parameter of mlflow.tracing.context, which causes TraceMetadataKey.TRACE_SESSION (the key 'mlflow.trace.session') to appear in trace.info.trace_metadata.

*   Simulation metadata must be stored in trace.info.trace_metadata (not in trace.info.request_metadata and not in span attributes).

*   The prediction function must no longer be wrapped in a tracing decorator that creates an extra root span (such as a 'simulation_turn_N' wrapper span). No span attributes for simulation goal, persona, or context should be set on any root span.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.