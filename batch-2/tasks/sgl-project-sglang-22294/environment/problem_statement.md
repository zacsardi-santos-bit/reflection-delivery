## Description

Loading external corpora into the speculative decoding system has a serious correctness problem: if a corpus load fails for any reason — such as exceeding the configured token budget — all previously loaded corpora are wiped out, not just the one that failed. This is because the error-handling path currently clears the entire external corpus state rather than only rolling back the staged, in-progress load.

Additionally, there is no mechanism to track how much of the token budget has already been consumed by previously loaded corpora, so it is impossible to know in advance whether a new load will exceed the limit. There is also no protection against loading a second corpus under an ID that already exists, which can silently replace the original data.

## Expected Behavior

- A failed corpus load must only clean up its own staged data. All previously committed corpora must remain intact and available for matching after a failed load.
- Duplicate corpus IDs must be explicitly rejected with a clear error. Attempting to load a corpus with an ID that is already in use must raise an error without modifying the existing corpus.
- The system must expose the remaining token capacity so callers can check how much budget is left before initiating a new load.
- Removing a corpus must free its tokens from the budget so that capacity becomes available again for future loads.

## Why This Matters

Without these fixes, a single failed load can silently destroy all previously loaded corpora, leading to hard-to-debug correctness issues. Users operating in resource-constrained environments need reliable budget tracking and safe error recovery to build production-quality pipelines on top of this API.
