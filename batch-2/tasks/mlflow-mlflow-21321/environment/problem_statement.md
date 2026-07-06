## Description

The conversation simulator currently attaches simulation metadata to traces by wrapping each call to the user's prediction function in a tracing decorator. This decorator creates an extra root span that appears in trace visualizations alongside the user's actual prediction spans, which is confusing and pollutes the trace structure. Additionally, simulation metadata such as goal, persona, and session information is stored in span attributes and in a metadata field that is inconsistent with how other tracing features store data.

## Expected Behavior

- Simulation metadata (goal, persona, turn number, session ID, and guidelines) should be stored in the standard trace-level metadata location, not in root span attributes or in a deprecated metadata field.
- Each simulation turn should attach metadata to traces using the tracing context API, not by wrapping the prediction function in a tracing decorator.
- The tracing context mechanism should be used once per simulation turn.
- The trace session ID should be passed through the tracing context's session parameter so that it appears under the standard session metadata key.
- Long goal and persona strings should continue to be truncated to the maximum metadata length limit.
- No extra wrapper spans should be created around the prediction function's invocation.

## Why This Matters

Users inspecting traces from simulated conversations currently see an extra "wrapper" span that doesn't represent real prediction logic. Simulation metadata should appear in the right place (trace-level metadata) without cluttering the span structure of the underlying prediction function's trace.
