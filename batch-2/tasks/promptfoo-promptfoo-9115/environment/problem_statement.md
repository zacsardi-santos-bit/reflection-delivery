## Description

The redteam setup skill and its reference documentation recommend starting with a basic strategy for the initial security test generation pass. This is not the best default — a more capable jailbreak approach should be recommended from the beginning rather than requiring users to manually escalate after verifying the first batch of test cases.

Additionally, the reference documentation does not clearly distinguish between scanning with multiple inputs (a structural concern about the target configuration) and running multi-turn conversational tests (a concern about stateful session handling). This confusion can lead users to apply the wrong strategy for stateful conversational targets.

## Expected Behavior

- The skill instructions should recommend the more capable jailbreak technique as the default strategy for initial setup and generation passes, not the basic approach.
- There should be a separate, explicit recommendation for when targets are stateful and support multi-turn conversations.
- The reference examples should include a clear note that multi-input testing is not the same as multi-turn testing, along with example configuration showing the appropriate strategy for stateful conversational targets.
- All example configurations in the reference documentation and the OpenAPI helper script should use the new default strategy instead of the basic one.

## Why This Matters

Users who follow the documented guidance for their first redteam setup will get better security test coverage from the start. The clarification between multi-input and multi-turn setups will also prevent a common misconfiguration for teams working with conversational AI systems.
