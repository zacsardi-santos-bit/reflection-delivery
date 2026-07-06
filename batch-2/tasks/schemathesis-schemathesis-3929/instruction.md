I'm working with Schemathesis to test a Go-based API and I've noticed it doesn't understand the validation error responses that my API returns.

*   GoValidatorParser must be importable from schemathesis.core.error_feedback.parsers.go_validator.

*   GoValidatorParser.can_parse(body) must return True for the go-playground/validator default text envelope: a dict with a single 'error' key whose value is a non-empty string matching the pattern 'Key: \'Body.X\' Error:Field validation for \'X\' failed on the \'Y\' tag', including multi-line variants with newline-separated errors.

*   GoValidatorParser.can_parse(body) must return True for the go-playground/validator structured envelope: a dict with an 'errors' key whose value is a non-empty list where at least one item is a dict containing both 'tag' and 'namespace' keys (and not shaped like AJV or Zod errors).

*   GoValidatorParser.can_parse(body) must return False for: empty dict, None, empty string, list, a dict with 'error' key whose value does not match the go-validator pattern, a dict with 'error' key whose value is an empty string, a dict with 'errors' key whose value is an empty list, AJV-shaped errors (items with 'keyword' and 'instancePath' keys), Zod-shaped errors (items with 'code' and 'path' keys), items missing 'namespace' key, DRF-shaped responses, and Pydantic-shaped responses.

*   GoValidatorParser.parse(operation, body) must return an empty tuple for bodies that do not match the go-validator envelope.

*   For the default text envelope, GoValidatorParser.parse must produce observations only for constraint tags that are self-describing without a parameter: 'required' → ObservationKind.MUST_NOT_BE_BLANK with None payload; 'email' → ObservationKind.FORMAT with FormatPayload(name='email'); 'url' → ObservationKind.FORMAT with FormatPayload(name='uri'); 'uuid' → ObservationKind.FORMAT with FormatPayload(name='uuid'). Tags that require a numeric parameter ('min', 'max', 'gte', 'lte', 'gt', 'lt', 'oneof', 'alphanum') must be silently dropped when parsed from the default text format.

*   For the structured envelope, GoValidatorParser.parse must map tags to observations as follows: 'required' → ObservationKind.MUST_NOT_BE_BLANK (payload None); 'email' → ObservationKind.FORMAT FormatPayload(name='email'); 'url' → ObservationKind.FORMAT FormatPayload(name='uri'); 'uuid' → ObservationKind.FORMAT FormatPayload(name='uuid'); 'datetime' with param '2006-01-02' → ObservationKind.FORMAT FormatPayload(name='date'); 'datetime' with param '2006-01-02T15:04:05Z' → ObservationKind.FORMAT FormatPayload(name='date-time'); 'min' or 'max' with kind 'string' or 'slice' and a numeric param → ObservationKind.SIZE_BOUND with SizeBoundPayload(min=N, max=None) or SizeBoundPayload(min=None, max=N) respectively; 'len' with kind 'string' and a numeric param → ObservationKind.SIZE_BOUND with SizeBoundPayload(min=N, max=N); 'gte' → ObservationKind.NUMERIC_BOUND NumericBoundPayload(bound=float(param), direction=BoundDirection.MIN, exclusive=False); 'lte' → ObservationKind.NUMERIC_BOUND NumericBoundPayload(bound=float(param), direction=BoundDirection.MAX, exclusive=False); 'gt' → ObservationKind.NUMERIC_BOUND NumericBoundPayload(bound=float(param), direction=BoundDirection.MIN, exclusive=True); 'lt' → ObservationKind.NUMERIC_BOUND NumericBoundPayload(bound=float(param), direction=BoundDirection.MAX, exclusive=True); 'min' with a numeric kind (e.g. 'int', 'float64') and a numeric param → ObservationKind.NUMERIC_BOUND NumericBoundPayload(bound=float(param), direction=BoundDirection.MIN, exclusive=False); 'oneof' with a non-empty, non-whitespace-only param → ObservationKind.ENUM EnumPayload(values=tuple(param.split())); 'alphanum' and unknown tags → silently dropped.

*   In the structured envelope, GoValidatorParser.parse must silently drop any issue entry that is malformed: missing 'kind' key, empty namespace, non-numeric param when a numeric param is required ('min', 'max', 'gte', 'lte', 'gt', 'lt', 'len'), empty param when param is required, whitespace-only param for 'oneof', 'oneof' with empty param, 'len' without 'param' key, 'datetime' with empty param, unknown tags, and kind values that are neither a size kind ('string', 'slice') nor a numeric kind for tags that require kind disambiguation.

*   GoValidatorParser.parse must build the parameter path from the 'namespace' field by stripping the 'Body.' prefix, splitting on '.', converting '[N]' array-index segments to integers, and lower-casing the first letter of each alphabetic segment. Path entries must be dropped if: the namespace is empty, a path segment starts with '[', or the resulting path is empty (struct-level namespace like 'Body' with no sub-field).

*   For GET operations, GoValidatorParser.parse must set the observation location to ParameterLocation.QUERY.

*   The parsers AjvParser, AspNetParser, LaravelParser, RailsParser, PydanticParser, and JacksonParser must each return False from can_parse() for every body that GoValidatorParser.can_parse() accepts.


*   Interface details: Type: Class
Name: GoValidatorParser
Location: src/schemathesis/core/error_feedback/parsers/go_validator.py
Description: Error feedback parser that recognises and converts go-playground/validator error responses into constraint observations. Supports both the default (text) envelope format and the structured (JSON list) envelope format.
Signature: can_parse(body: Any) -> bool
Signature: parse(operation: Any, body: Any) -> tuple[Observation, ...]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.