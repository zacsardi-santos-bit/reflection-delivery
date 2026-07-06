I'm working on improving the tracing destination management in MLflow.

*   The `UnityCatalog` class must be importable from `mlflow.entities.trace_location`. It must accept `catalog_name`, `schema_name`, and `table_prefix` constructor arguments and expose a `table_prefix` attribute.

*   The `_MLFLOW_TRACE_USER_DESTINATION` instance in `mlflow/tracing/provider.py` must expose a `set_experiment_derived(destination, experiment_id: str)` method that caches a destination tied to a specific experiment ID, and store it in a `_experiment_derived` attribute (which must not be None after calling this method).

*   The `_MLFLOW_TRACE_USER_DESTINATION.get()` method must return destinations in priority order: context-local (set via `set(context_local=True)`) > global (set via `set()`) > experiment-derived (if the active experiment ID matches the cached one) > environment variable fallback.

*   When `_MLFLOW_TRACE_USER_DESTINATION.get()` finds an experiment-derived destination whose experiment ID does not match the currently active experiment, it must fall through to the environment variable fallback without clearing or mutating the `_experiment_derived` cache.

*   When `_MLFLOW_TRACE_USER_DESTINATION.get()` attempts to validate the experiment-derived destination and `_get_experiment_id()` raises a runtime exception, the method must catch the exception and return the experiment-derived destination as a fallback rather than propagating the error.

*   The `_MLFLOW_TRACE_USER_DESTINATION.reset()` method must clear the global value, the context-local value, and the `_experiment_derived` cache.

*   When `MLFLOW_TRACING_DESTINATION` is set to a three-part dot-separated string (e.g. `catalog.schema.prefix`), calling `_MLFLOW_TRACE_USER_DESTINATION.get()` must raise `MlflowException` with a message matching: "Unity Catalog table-prefix destinations (<catalog_name>.<schema_name>.<table_prefix>) are not supported".

*   Calling `mlflow.tracing.set_destination` with a `UCSchemaLocation` instance must log exactly one warning whose message contains the string "Passing `UCSchemaLocation` to `mlflow.tracing.set_destination` is deprecated".

*   Calling `mlflow.tracing.set_destination` with a `UnityCatalog` instance must raise `MlflowException` with a message matching: "UnityCatalog table-prefix destinations are not supported by `mlflow.tracing.set_destination`".

*   `DatabricksUCTableSpanProcessor.on_start()` must resolve the active Unity Catalog destination by reading from `_MLFLOW_TRACE_USER_DESTINATION` directly (not via the `get_active_spans_table_name` helper). When no destination is set, it must raise `MlflowException` with a message containing "Unity Catalog spans table name is not set".

*   When `DatabricksUCTableSpanProcessor.on_start()` resolves a `UCSchemaLocation` destination, it must use `destination.catalog_name`, `destination.schema_name`, and `destination._otel_spans_table_name` to construct the trace location.


*   Interface details: Type: Class
Name: UnityCatalog
Location: mlflow/entities/trace_location.py
Description: Represents a Unity Catalog destination with a table prefix. Must accept `catalog_name`, `schema_name`, and `table_prefix` as constructor arguments and expose them as attributes.
Signature: UnityCatalog(catalog_name: str, schema_name: str, table_prefix: str)

Type: Class
Name: UCSchemaLocation
Location: mlflow/entities/trace_location.py
Description: Represents a Unity Catalog schema-level destination. Must accept `catalog_name` and `schema_name` as constructor arguments and expose them as attributes. The `_otel_spans_table_name` attribute must be directly settable on the instance.
Signature: UCSchemaLocation(catalog_name: str, schema_name: str)

Type: Variable (instance)
Name: _MLFLOW_TRACE_USER_DESTINATION
Location: mlflow/tracing/provider.py
Description: Global registry instance for the active user-specified trace destination. Must support the methods described below and expose the `_experiment_derived` attribute.

  Methods on _MLFLOW_TRACE_USER_DESTINATION:

  set(value, context_local: bool = False) -> None
    Sets the active destination. If context_local=False (default), sets globally. If context_local=True, sets per-task/thread via a ContextVar.

  get() -> TraceLocationBase | None
    Returns the active destination using the following priority order:
    1. Context-local value (highest priority)
    2. Global value
    3. Experiment-derived value — only if active experiment ID matches the stored experiment_id; if the experiment check raises an exception, return the experiment-derived value as a fallback
    4. Environment variable fallback via MLFLOW_TRACING_DESTINATION (lowest priority)
    When the environment variable contains a three-part dot-separated path (catalog.schema.prefix), this method must raise MlflowException with a message containing: "Unity Catalog table-prefix destinations (<catalog_name>.<schema_name>.<table_prefix>) are not supported"

  reset() -> None
    Clears the global value, the context-local value, and the _experiment_derived cache.

  set_experiment_derived(value, experiment_id: str | None = None) -> None
    Caches a destination tied to the given experiment_id. After calling this method, the `_experiment_derived` attribute must be non-None.

  Attribute: _experiment_derived
    Must be non-None after set_experiment_derived() is called. Must be reset to None by reset(). Must NOT be mutated or cleared by get() even when the experiment ID does not match the active experiment.

Type: Function
Name: set_destination
Location: mlflow/tracing/provider.py (exposed as mlflow.tracing.set_destination)
Description: Sets the active tracing destination. Must raise MlflowException when called with a UnityCatalog instance (message must match: "UnityCatalog table-prefix destinations are not supported by `mlflow.tracing.set_destination`"). Must log exactly one warning when called with a UCSchemaLocation instance; the warning message must contain: "Passing `UCSchemaLocation` to `mlflow.tracing.set_destination` is deprecated".

Type: Class
Name: DatabricksUCTableSpanProcessor
Location: mlflow/tracing/processor/uc_table.py
Description: Span processor for Unity Catalog trace export. The on_start() method must resolve the active destination by importing and calling _MLFLOW_TRACE_USER_DESTINATION.get() directly (not via get_active_spans_table_name). When the destination is a UCSchemaLocation, it must use destination.catalog_name, destination.schema_name, and destination._otel_spans_table_name to build the trace location. When the resolved destination is neither UCSchemaLocation nor UnityCatalog (including None), on_start() must raise MlflowException with a message containing "Unity Catalog spans table name is not set".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.