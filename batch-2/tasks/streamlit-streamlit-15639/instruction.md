I'm building a Streamlit app where my selectbox options are custom objects defined inside the app script.

*   resolve_value_against_options must be publicly exported from streamlit.elements.lib.options_selector_utils and importable by name.

*   When resolve_value_against_options is called with a None current_value, it must return (None, False) without invoking format_func or triggering any reset.

*   When resolve_value_against_options is called with a value whose formatted label (via format_func) is present in formatted_option_to_option_index, it must return (current_value, False) with no reset.

*   When resolve_value_against_options is called with a value whose formatted label is absent from formatted_option_to_option_index (and format_func did not raise), it must return (opt[default_index], True); if default_index is None, it must return (None, True).

*   When resolve_value_against_options is called with a format_func that raises an exception for the given value, and incoming_serialized_value is a key present in formatted_option_to_option_index, it must return (opt[formatted_option_to_option_index[incoming_serialized_value]], False) — the current option instance at that index, not the stale passed-in value.

*   When resolve_value_against_options is called with a format_func that raises and incoming_serialized_value is None or not a key in formatted_option_to_option_index, it must return (opt[default_index], True); if default_index is None, it must return (None, True).

*   RegisterWidgetResult must have a field incoming_serialized_value: str | None = None in addition to its existing value and value_changed fields. It must be constructable as RegisterWidgetResult(value, value_changed, incoming_serialized_value='...') and the field must be readable as .incoming_serialized_value.

*   maybe_coerce_enum must preserve the incoming_serialized_value field of the input RegisterWidgetResult in the returned RegisterWidgetResult; only the value field should be changed to the coerced enum member.

*   maybe_coerce_enum_sequence must preserve the incoming_serialized_value field of the input RegisterWidgetResult in the returned RegisterWidgetResult; only the value field should be changed to the coerced sequence.

*   st.selectbox must preserve the user's selection across reruns when a custom format_func raises for the stored (stale) value but the stored value's serialized wire label matches a current option; the widget must return the current run's option instance without resetting to the default.

*   st.selectbox must preserve enum option selections when enum coercion is disabled and a custom format_func raises on the stale enum value, by using the wire-label fallback to identify the matching current option.

*   When a widget with a string-type serialized value is registered, the runtime must capture the stored wire label before applying this run's serializer and expose it as incoming_serialized_value in the returned RegisterWidgetResult.


*   Interface details: Type: Function
Name: resolve_value_against_options
Location: lib/streamlit/elements/lib/options_selector_utils.py
Signature: resolve_value_against_options(current_value, opt, formatted_option_to_option_index, default_index, key, format_func=str, incoming_serialized_value=None) -> tuple[T | None, bool]
Description: Resolves the current stored selectbox value against the current available options, returning a (resolved_value, needs_reset) tuple. Parameters:
  - current_value: the stored widget value (Any or None)
  - opt: Sequence of current valid options
  - formatted_option_to_option_index: dict[str, int] mapping formatted option labels to their index in opt
  - default_index: int | None — the fallback index when the value must be reset
  - key: str | int | None — the widget key used for session-state side effects (always None in test calls)
  - format_func: Callable[[Any], str] — defaults to str; used to compute the value's formatted label
  - incoming_serialized_value: str | None — the raw wire label of the stored value; used as fallback identity when format_func raises
  Behavioral contract:
  - If current_value is None: return (None, False).
  - If format_func(current_value) succeeds and the result is in formatted_option_to_option_index: return (current_value, False).
  - If format_func(current_value) succeeds and the result is NOT in formatted_option_to_option_index: reset to (opt[default_index], True), or (None, True) if default_index is None.
  - If format_func(current_value) raises an exception and incoming_serialized_value is a key in formatted_option_to_option_index: return (opt[formatted_option_to_option_index[incoming_serialized_value]], False) — the CURRENT option instance at that index, not the passed-in stale value.
  - If format_func(current_value) raises and incoming_serialized_value is None or not in formatted_option_to_option_index: reset to (opt[default_index], True), or (None, True) if default_index is None.

Type: Function
Name: maybe_coerce_enum
Location: lib/streamlit/elements/lib/options_selector_utils.py
Signature: maybe_coerce_enum(register_widget_result: RegisterWidgetResult, type_: type, options: list) -> RegisterWidgetResult
Description: Existing function that coerces an Enum value stored in a RegisterWidgetResult. Must preserve ALL fields of the input RegisterWidgetResult (including incoming_serialized_value) in the returned result; only the value field should change to the coerced enum member.

Type: Function
Name: maybe_coerce_enum_sequence
Location: lib/streamlit/elements/lib/options_selector_utils.py
Signature: maybe_coerce_enum_sequence(register_widget_result: RegisterWidgetResult, type_: type, options: list) -> RegisterWidgetResult
Description: Existing function that coerces a sequence of Enum values stored in a RegisterWidgetResult. Must preserve ALL fields of the input RegisterWidgetResult (including incoming_serialized_value) in the returned result; only the value field should change to the coerced sequence.

Type: Class
Name: RegisterWidgetResult
Location: lib/streamlit/runtime/state/common.py
Description: Dataclass holding the result of registering a widget with the runtime. Must have an incoming_serialized_value field. Tests directly construct it as RegisterWidgetResult(value, value_changed, incoming_serialized_value="...") and then read back .incoming_serialized_value.
Signature:
  value: T_co  — the deserialized widget value
  value_changed: bool  — whether the value changed this run
  incoming_serialized_value: str | None = None  — the raw wire label of the stored value before this run's serializer was applied; None when unavailable


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.