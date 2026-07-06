## Description

The Databricks operators (for creating jobs, submitting runs, and running now) currently merge the raw JSON payload with individually-specified named parameters at construction time, storing the result back onto the operator. This design causes silent data corruption on task retries and deferred executions: because the merged, processed payload overwrites the original template field, Airflow cannot re-render the original templates when rescheduling or retrying the task — it instead uses the already-evaluated, stale value.

Additionally, payload validation (type checking and constraint checking such as conflicting parameter combinations) currently runs inside the constructor. This means constructing one of these operators with an invalid payload raises immediately, preventing the operator from even being serialized. Validation should instead fire at execution time, matching the lifecycle Airflow expects.

## Expected Behavior

- The operators must provide a method that computes and returns the merged payload on demand (combining the JSON field with named parameters) without modifying any stored state.
- The execution method must perform all validation and merging at run time, not at construction time.
- After execution completes (or raises), all original template fields must remain in their pre-execution state so that a retry can re-render templates from scratch.
- Parameters injected into the payload (such as Airflow task params) must reach the Databricks API call, but must not be written back into the operator's template fields.
- Payload validation failures (invalid types, conflicting arguments, malformed JSON strings, missing required fields) must raise before any Databricks API call is made and before the API client is instantiated.
- On repair runs after a deferred execution, the merged payload (including named parameters) must be rebuilt from the template fields rather than read from a previously mutated field.

## Why This Matters

Tasks using these operators will silently use wrong parameter values on their second and subsequent attempts if template fields are mutated on the first attempt. Moving merge and validation to execution time restores correct retry semantics and makes the operators safe to serialize.
