I'm working on improving the error feedback system in a testing tool that already knows how to parse validation error responses from Java/Spring and Pydantic-based APIs.

*   The `TypeMismatchPayload` dataclass must rename its field from `java_type` to `type_name`. All code that constructs or reads `TypeMismatchPayload` must use `TypeMismatchPayload(type_name=...)` instead of `TypeMismatchPayload(java_type=...)`.

*   The `parse()` method on all response parser classes (`SpringParser`, `JacksonParser`, `PydanticParser`, `DRFParser`) must accept `operation: APIOperation` as a keyword argument instead of `operation_label: str`. The operation label is obtained from `operation.label`.

*   A new `DRFParser` class must be created at `schemathesis/core/error_feedback/parsers/drf.py` and registered in the `PARSERS` registry so that `DRFParser` appears in `PARSERS.get_all()`.

*   `DRFParser.can_parse(body)` must return `True` for bodies that match DRF error envelopes: a non-empty dict where at least one value is a list of strings (flat or nested), a dict containing a `non_field_errors` key, a dict with list-of-dict values (e.g. list-serializer errors), or a dict with string-digit keys pointing to lists. It must return `False` for: empty dict, `None`, empty string, empty list, top-level list, a dict whose only key is `detail` with a string value, a dict with scalar int or bool leaf values, and any non-dict/non-list. It must also return `False` (not hang) for pathologically deeply nested bodies (depth cap must be enforced).

*   A module-level `_walk(body, path=())` function must be defined in `schemathesis/core/error_feedback/parsers/drf.py`. It must recursively yield `(path_tuple, message_str)` pairs from DRF error bodies. It must: skip any key named `non_field_errors` at any depth; convert ASCII digit string dict keys (e.g. `"0"`, `"2"`) to integer indices in the path; skip non-string dict keys entirely; when a list contains strings, yield each non-empty string with the current path; when a list contains dicts (no strings), enumerate them with integer indices and recurse, skipping `None` and empty-dict items. Leaves that are empty lists, empty strings, `None`-only lists, or non-list scalars must produce no output.

*   A module-level `_location_for_method(method: str) -> ParameterLocation` function must be defined in `schemathesis/core/error_feedback/parsers/drf.py`. It must return `ParameterLocation.QUERY` for GET, DELETE, and HEAD (case-insensitive), and `ParameterLocation.BODY` for all other methods including POST, PUT, PATCH, OPTIONS, unknown verbs, and lowercase inputs.

*   A module-level `_classify(message: str)` function must be defined in `schemathesis/core/error_feedback/parsers/drf.py`. It must return a `(ObservationKind, payload)` tuple for recognized DRF error messages and `None` for unrecognized ones. Exact literal matches: `"This field is required."`, `"This field may not be blank."`, `"This field may not be null."` → `MUST_NOT_BE_BLANK, None`; `"Enter a valid email address."` → `FORMAT, FormatPayload(name="email")`; `"Enter a valid URL."` → `FORMAT, FormatPayload(name="uri")`; `"Must be a valid UUID."` → `FORMAT, FormatPayload(name="uuid")`; `"A valid integer is required."` → `TYPE_MISMATCH, TypeMismatchPayload(type_name="integer")`; `"A valid number is required."` → `TYPE_MISMATCH, TypeMismatchPayload(type_name="number")`; `"Must be a valid boolean."` → `TYPE_MISMATCH, TypeMismatchPayload(type_name="boolean")`. Prefix matches: `"Date has wrong format."` → `FORMAT, FormatPayload(name="date")`; `"Datetime has wrong format."` → `FORMAT, FormatPayload(name="date-time")`; `"Time has wrong format."` → `FORMAT, FormatPayload(name="time")`; `"Expected a list of items but got type"` → `TYPE_MISMATCH, TypeMismatchPayload(type_name="array")`; `"Expected a dictionary of items"` → `TYPE_MISMATCH, TypeMismatchPayload(type_name="object")`. Regex matches for size bounds: `"Ensure this field has at least N characters."` and `"Ensure this value has at least N characters."` (with optional `(it has N)` suffix) → `SIZE_BOUND, SizeBoundPayload(min=N, max=None)`; `"Ensure this field has no more than N characters."`, `"Ensure this field has at most N characters."`, and `"Ensure this value has at most N characters."` (with optional suffix) → `SIZE_BOUND, SizeBoundPayload(min=None, max=N)`; `"Ensure this field has at least N elements."` → `SIZE_BOUND, SizeBoundPayload(min=N, max=None)`; `"Ensure this field has no more than N elements."` (singular `element` also accepted) → `SIZE_BOUND, SizeBoundPayload(min=None, max=N)`. Regex matches for numeric bounds: `"Ensure this value is greater than or equal to N."` → `NUMERIC_BOUND, NumericBoundPayload(bound=float(N), direction=BoundDirection.MIN, exclusive=False)`; `"Ensure this value is greater than N."` → `NUMERIC_BOUND, ..., exclusive=True`; `"Ensure this value is less than or equal to N."` → `NUMERIC_BOUND, direction=BoundDirection.MAX, exclusive=False`; `"Ensure this value is less than N."` → `NUMERIC_BOUND, direction=BoundDirection.MAX, exclusive=True`. Negative numbers and decimals must be handled in numeric patterns.

*   `DRFParser.parse(operation, body)` must return a tuple of `Observation` objects. It must use `_location_for_method(operation.method)` to determine location, walk the body with `_walk`, classify each message with `_classify`, skip messages where `_classify` returns `None`, and produce `Observation` objects with `operation_label=operation.label`, the computed location, and the path/kind/raw_message/payload from the walk and classification. Entries under `non_field_errors` must produce no observations (empty tuple). Unrecognized messages must be skipped.

*   `TypeMismatchAdjustment.apply()` must handle both DRF-style JSON Schema type tokens (`"integer"`, `"number"`, `"boolean"`, `"array"`, `"object"`) and Java FQN type names. When `type_name` is a JSON Schema type token: if the property's existing `type` is a scalar that does not match `type_name` and is not `"object"` or `"array"`, rewrite `prop["type"] = type_name`; if the property already has the correct type, do nothing; if the property uses anyOf/oneOf/allOf or a type-list, skip conservatively. When `type_name` is not a JSON Schema type token, apply the existing format-injection logic via `_JAVA_TYPE_TO_FORMAT`.

*   `ObservationPayload` must be importable from `schemathesis.core.error_feedback`.


*   Interface details: Type: Class
Name: DRFParser
Location: src/schemathesis/core/error_feedback/parsers/drf.py
Description: Parser for Django REST Framework validation error envelopes. Must be decorated/registered with @PARSERS.register. Implements the ResponseParser protocol.
Signature:
  can_parse(self, *, body: object) -> bool
  parse(self, *, operation: APIOperation, body: object) -> tuple[Observation, ...]

Type: Function
Name: _walk
Location: src/schemathesis/core/error_feedback/parsers/drf.py
Signature: _walk(body: object, path: tuple[str | int, ...] = ()) -> Iterator[tuple[tuple[str | int, ...], str]]
Description: Recursively walks a DRF error body yielding (path_tuple, message_string) pairs. Skips `non_field_errors` keys, converts ASCII digit string keys to integer indices, skips non-string dict keys, skips empty strings/None/empty-dict items.

Type: Function
Name: _location_for_method
Location: src/schemathesis/core/error_feedback/parsers/drf.py
Signature: _location_for_method(method: str) -> ParameterLocation
Description: Maps an HTTP method string to a ParameterLocation. Returns ParameterLocation.QUERY for GET, DELETE, HEAD (case-insensitive); ParameterLocation.BODY for everything else.

Type: Function
Name: _classify
Location: src/schemathesis/core/error_feedback/parsers/drf.py
Signature: _classify(message: str) -> tuple[ObservationKind, ObservationPayload | None] | None
Description: Classifies a DRF error message string into an (ObservationKind, payload) pair. Returns None for unrecognized messages.

Type: Dataclass
Name: TypeMismatchPayload
Location: src/schemathesis/core/error_feedback/store.py
Description: Frozen dataclass carrying a framework-specific type identifier. The field is named `type_name` (not `java_type`). Used for both Java FQN types (e.g. "java.time.LocalDate") and JSON Schema type tokens (e.g. "integer", "boolean", "number", "array", "object").
Signature: TypeMismatchPayload(type_name: str)

Type: Export
Name: ObservationPayload
Location: src/schemathesis/core/error_feedback/__init__.py (exported from schemathesis.core.error_feedback)
Description: The ObservationPayload type must be importable from schemathesis.core.error_feedback.

Type: Protocol method
Name: parse (on ResponseParser protocol)
Location: src/schemathesis/core/error_feedback/parsers/__init__.py
Signature: parse(self, *, operation: APIOperation, body: object) -> tuple[Observation, ...]
Description: The ResponseParser protocol's parse() method now accepts `operation: APIOperation` instead of `operation_label: str`. All parser implementations (SpringParser, JacksonParser, PydanticParser, DRFParser) must use this updated signature.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.