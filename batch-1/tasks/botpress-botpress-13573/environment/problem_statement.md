## Description

The cognitive package needs a composable middleware pipeline mechanism that allows multiple processing steps to be chained together in a well-defined, ordered sequence. Right now there is no standard way to compose handlers that can pass values forward through the chain, introduce or clear error states, short-circuit remaining steps, or respect cancellation signals.

## Expected Behavior

- Handlers registered in a pipeline should execute in the exact order they were added.
- Each handler should receive both the current error state and current value, then decide whether to continue the chain or stop early.
- A handler can pass a modified value or error to the next step in the chain.
- A handler can choose to bypass all remaining steps and immediately settle the result.
- A handler that receives an error can clear it and resume normal resolution.
- When a cancellation signal is triggered before or during pipeline execution, the operation should reject immediately rather than continuing.
- If an error passes through the entire chain without being cleared, the pipeline should reject with that error.
- If no error remains at the end of the chain, the pipeline should resolve with the final accumulated value.

## Why This Matters

This pattern is needed to support request and response processing in the cognitive client — for example, attaching model selection logic or fallback handling before a request goes out, and post-processing responses before they are returned. A reusable pipeline abstraction makes this composable and testable in isolation.
