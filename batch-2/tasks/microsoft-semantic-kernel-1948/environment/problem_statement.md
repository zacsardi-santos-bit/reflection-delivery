## Description

The semantic kernel planning module currently lacks a simple, single-action planner. Existing planners focus on generating multi-step sequences of operations, but there are many use cases where a developer just wants the kernel to pick the single most relevant registered function and fill in its parameters automatically — without orchestrating a full pipeline.

## Expected Behavior

- A new planner type should be available that, given a natural-language goal, selects exactly one function from the kernel's registered skills and returns a plan ready to execute with the appropriate parameters populated.
- If no kernel is provided, the planner should immediately reject the request with a clear planning error.
- If the kernel does not have a language model service configured, construction should fail with a meaningful error.
- If the goal is empty, the planner should raise a planning error rather than forwarding a meaningless request to the model.
- If the language model returns a response that cannot be interpreted as a valid plan (e.g., malformed or missing the expected structure), the planner should raise a planning error.
- The planner should also be able to enumerate all available functions as a formatted string listing each skill and function name, for use in prompt construction.

## Why This Matters

Developers who want a lightweight "pick the best function for this task" workflow currently have no appropriate planner to use. This change adds a missing, simpler planner variant that covers single-action intent detection without forcing them to use a full multi-step planning approach.
