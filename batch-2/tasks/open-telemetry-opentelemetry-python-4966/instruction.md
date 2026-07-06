I'm working on the OpenTelemetry Python SDK and I'd like to add per-meter enable/disable control to the metrics subsystem, similar to what already exists for tracers.

*   The _MeterConfig class must be a dataclass with a boolean `is_enabled` field (default True) and a classmethod `default()` that returns an instance with `is_enabled=True`.

*   The _ProxyMeterConfig class must wrap a _MeterConfig instance, expose its `is_enabled` value via a property, and support in-place updates via an `update(config: _MeterConfig)` method that replaces the underlying config object.

*   The _default_meter_configurator function must accept an InstrumentationScope and return a _MeterConfig with is_enabled=True for any scope.

*   The _disable_meter_configurator function must accept an InstrumentationScope and return a _MeterConfig with is_enabled=False for any scope.

*   The _RuleBasedMeterConfigurator class must be callable with an InstrumentationScope. It must accept `rules` (a list of predicate/_MeterConfig pairs) and `default_config` as keyword-only constructor parameters. It must apply first-match-wins logic: iterating rules in order, returning the first matching config; if no rule matches, returning `default_config`.

*   MeterProvider must accept a keyword-only `_meter_configurator` parameter. If None, it defaults to _default_meter_configurator. The value must be stored as the `_meter_configurator` attribute. Each call to `get_meter()` must invoke the configurator with the meter's InstrumentationScope to determine whether the resulting meter is enabled.

*   When a meter configurator raises an exception during get_meter(), MeterProvider must log at ERROR level and fall back to the default (enabled) configuration for that meter.

*   MeterProvider must expose a `_set_meter_configurator(*, meter_configurator)` method. Calling it must update the stored configurator, re-apply it to all existing meters, and cause all subsequently created meters to use the new configurator. When the configurator raises during re-evaluation of existing meters, MeterProvider must log at ERROR level and keep the meter enabled.

*   Meter must expose a `_is_enabled() -> bool` method that returns the current enabled state of the meter (determined by the most recently applied configurator result).

*   When a meter is disabled, all synchronous instrument operations (counter add, up-down counter add, histogram record, gauge set) must silently skip sending measurements to the measurement consumer.

*   When a meter is disabled, all asynchronous instrument callbacks (observable counter, observable gauge, observable up-down counter) must not invoke their registered callback functions.

*   A meter that starts enabled and is later disabled (via _set_meter_configurator) must immediately stop forwarding measurements for all instruments already created on that meter. A meter that is subsequently re-enabled must resume forwarding measurements.

*   The _scope_name_matches_glob function must be located in opentelemetry.sdk.util.instrumentation (not in opentelemetry.sdk.trace). It must accept a glob pattern string and return a predicate Callable[[InstrumentationScope], bool] that matches the scope name using fnmatch semantics, including exact matches and wildcard patterns.

*   A new environment variable constant OTEL_PYTHON_METER_CONFIGURATOR must be defined in opentelemetry.sdk.environment_variables with the string value 'OTEL_PYTHON_METER_CONFIGURATOR'.

*   A _get_meter_configurator() function must be added to opentelemetry.sdk._configuration that reads and returns the OTEL_PYTHON_METER_CONFIGURATOR environment variable value (or None if unset).

*   A _import_meter_configurator(meter_configurator_name) function must be added to opentelemetry.sdk._configuration. If the name is None or empty it returns None. It loads the configurator callable from the '_opentelemetry_meter_configurator' entry point group by the given name. On any failure it logs a WARNING and returns None.

*   The _init_metrics function must be updated to accept a `meter_configurator` keyword argument and pass it to MeterProvider. Its first positional parameter must be named `exporters_or_readers`; `resource` and `exporter_args_map` must be keyword arguments.

*   The _initialize_components function must be updated to accept a `meter_configurator` keyword argument. When not provided, it must auto-discover a configurator via _get_meter_configurator()/_import_meter_configurator(). It must pass the resolved configurator to _init_metrics as a keyword argument.


*   Interface details: Type: Class
Name: _MeterConfig
Location: opentelemetry-sdk/src/opentelemetry/sdk/metrics/_internal/__init__.py
Description: Dataclass that holds meter configuration. Has an `is_enabled` boolean attribute (default True). Provides a `default()` classmethod that returns an instance with `is_enabled=True`.
Signature:
  __init__(is_enabled: bool = True)
  default() -> _MeterConfig  [classmethod]

Type: Class
Name: _ProxyMeterConfig
Location: opentelemetry-sdk/src/opentelemetry/sdk/metrics/_internal/__init__.py
Description: Proxy wrapper around a _MeterConfig that supports in-place updates. Exposes `is_enabled` as a property delegating to the underlying config, and an `update` method to swap the underlying config.
Signature:
  __init__(config: _MeterConfig)
  is_enabled -> bool  [property]
  update(config: _MeterConfig) -> None

Type: Function
Name: _default_meter_configurator
Location: opentelemetry-sdk/src/opentelemetry/sdk/metrics/_internal/__init__.py
Signature: _default_meter_configurator(meter_scope: InstrumentationScope) -> _MeterConfig
Description: Always returns a _MeterConfig with is_enabled=True.

Type: Function
Name: _disable_meter_configurator
Location: opentelemetry-sdk/src/opentelemetry/sdk/metrics/_internal/__init__.py
Signature: _disable_meter_configurator(meter_scope: InstrumentationScope) -> _MeterConfig
Description: Always returns a _MeterConfig with is_enabled=False.

Type: Class
Name: _RuleBasedMeterConfigurator
Location: opentelemetry-sdk/src/opentelemetry/sdk/metrics/_internal/__init__.py
Description: A callable configurator that evaluates a list of (predicate, _MeterConfig) rules in order and returns the config from the first matching rule. Returns default_config if no rule matches.
Signature:
  __init__(*, rules: Sequence[tuple[Callable[[InstrumentationScope], bool], _MeterConfig]], default_config: _MeterConfig)
  __call__(meter_scope: InstrumentationScope) -> _MeterConfig

Type: Method
Name: MeterProvider._set_meter_configurator
Location: opentelemetry-sdk/src/opentelemetry/sdk/metrics/_internal/__init__.py
Signature: _set_meter_configurator(*, meter_configurator: Callable[[InstrumentationScope], _MeterConfig]) -> None
Description: Replaces the current meter configurator on the MeterProvider, immediately re-evaluating the config for all existing meters and applying it to new meters. When the configurator raises during re-evaluation, logs an ERROR and falls back to the default (enabled) config for that meter.

Type: Method
Name: Meter._is_enabled
Location: opentelemetry-sdk/src/opentelemetry/sdk/metrics/_internal/__init__.py
Signature: _is_enabled() -> bool
Description: Returns whether this meter is currently enabled.

Type: Attribute
Name: MeterProvider._meter_configurator
Location: opentelemetry-sdk/src/opentelemetry/sdk/metrics/_internal/__init__.py
Description: Stores the currently active meter configurator callable. Readable as an attribute. MeterProvider constructor accepts a keyword-only parameter `_meter_configurator` (Optional); if None is passed, defaults to _default_meter_configurator.

Type: Function
Name: _scope_name_matches_glob
Location: opentelemetry-sdk/src/opentelemetry/sdk/util/instrumentation.py
Signature: _scope_name_matches_glob(glob_pattern: str) -> Callable[[InstrumentationScope], bool]
Description: Returns a predicate that matches an InstrumentationScope whose name matches the given glob pattern using fnmatch semantics. This function must exist in opentelemetry.sdk.util.instrumentation (moved/consolidated from opentelemetry.sdk.trace). Must NO LONGER be importable from opentelemetry.sdk.trace directly.

Type: Constant
Name: OTEL_PYTHON_METER_CONFIGURATOR
Location: opentelemetry-sdk/src/opentelemetry/sdk/environment_variables/__init__.py
Description: String constant equal to "OTEL_PYTHON_METER_CONFIGURATOR". Represents the environment variable name used to specify a custom meter configurator via entry point.

Type: Function
Name: _get_meter_configurator
Location: opentelemetry-sdk/src/opentelemetry/sdk/_configuration/__init__.py
Signature: _get_meter_configurator() -> str | None
Description: Reads the OTEL_PYTHON_METER_CONFIGURATOR environment variable and returns its value, or None if not set.

Type: Function
Name: _import_meter_configurator
Location: opentelemetry-sdk/src/opentelemetry/sdk/_configuration/__init__.py
Signature: _import_meter_configurator(meter_configurator_name: str | None) -> Callable | None
Description: Loads a meter configurator callable by name from the _opentelemetry_meter_configurator entry point group. Returns None if name is None or empty. If loading fails, logs a WARNING and returns None.

Type: Function
Name: _init_metrics
Location: opentelemetry-sdk/src/opentelemetry/sdk/_configuration/__init__.py
Signature: _init_metrics(exporters_or_readers, *, resource=None, exporter_args_map=None, meter_configurator=None)
Description: Updated to accept a `meter_configurator` keyword argument and pass it to MeterProvider. The first positional argument is named `exporters_or_readers`; `resource` and `exporter_args_map` are keyword arguments.

Type: Function
Name: _initialize_components
Location: opentelemetry-sdk/src/opentelemetry/sdk/_configuration/__init__.py
Signature: _initialize_components(..., meter_configurator=None)
Description: Updated to accept a `meter_configurator` keyword argument and pass it through to _init_metrics.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.