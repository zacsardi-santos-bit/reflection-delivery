## Description

MLflow currently has no built-in integration with Google's Agent Development Kit (ADK) for evaluating AI agents. Teams using ADK to assess whether an agent invoked the right tools or produced sufficiently similar text responses must handle this outside of MLflow's evaluation infrastructure. We need native scorers that wrap ADK's deterministic evaluators so they behave as first-class MLflow feedback objects.

There is also a bug in trace-based evaluation: when a scorer is given a recorded trace from a real agent run, the actual tool calls should be extracted automatically from the trace. Currently, users must explicitly re-specify the actual tool calls in the expectations dict — if they don't, the scorer silently treats the trajectory as empty and always reports failure.

## Expected Behavior

- A new **tool trajectory scorer** that compares actual versus expected tool calls using ADK's evaluator, supporting exact, in-order, and any-order matching strategies. It should return a pass/no-pass result with the numeric score and threshold in the metadata.
- A new **response similarity scorer** that measures text similarity between an actual response and a reference string, again returning a pass/no-pass result with score metadata.
- Both scorers should be recognized as third-party scorers (not directly registered or managed as MLflow-native scorers).
- When either scorer is called with a trace object from a real agent run, actual tool calls should be extracted automatically from the tool spans in the trace. An explicit override in the expectations should take precedence over trace extraction.
- A factory function that creates the appropriate scorer by name and passes through configuration options, raising an error for unrecognized metric names.
- Clear error feedback (with descriptive error messages) when required expectation fields are missing or when the underlying evaluator raises an exception.

## Why This Matters

Without this integration, ADK users who want to track evaluation results in MLflow experiments must manually bridge two systems. The trace extraction bug also means that any production agent evaluation based on recorded traces is silently broken, always reporting failure regardless of actual agent behavior.
