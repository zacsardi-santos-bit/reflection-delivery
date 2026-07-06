## Description

When evaluating AI agent behavior using trajectory-based assertions, it's currently possible to check which tools were invoked, but there's no way to verify *what arguments* were passed to those tools. This is a significant gap — an agent might call the right tool but with the wrong parameters, and such a failure would go undetected.

We need a new assertion type that allows users to assert that a specific tool call was made with expected argument values. The assertion should support:

- **Partial matching** (default): the tool call's arguments contain at least the specified key-value pairs
- **Exact matching**: the tool call's arguments match the expected arguments exactly
- **Inverse mode**: assert that a tool was NOT called with certain arguments
- **Tool name patterns**: match tool calls by glob-style name patterns in addition to exact name matching

Additionally, the trace data that feeds these evaluations currently does not capture tool arguments from trace spans. Trajectory steps need to be enriched with the argument data extracted from span attributes, making that information available for assertions.

## Expected Behavior

- Trajectory steps extracted from trace spans must include the parsed arguments for each tool call
- A new assertion type must allow verifying that a specific tool was called with expected argument values (partial or exact)
- The assertion must fail gracefully when a tool is found but no argument data was captured
- When argument data is missing from the assertion value, an informative error should be raised
- Only valid matching modes should be accepted; invalid modes must produce a clear error

## Why This Matters

Teams running automated evaluations of AI agents need fine-grained control over what they assert about agent behavior. The ability to verify argument values — not just tool names — is essential for catching subtle regressions where the right tool is called with incorrect inputs.
