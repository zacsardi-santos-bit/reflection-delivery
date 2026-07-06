## Description

The OpenAI Responses API model integration is missing support for pre-request token counting. Other model providers (Anthropic, Google, Bedrock Converse) already support the ability to count tokens before sending a request to enforce an input token limit proactively, but the OpenAI Responses model does not implement this capability.

## Expected Behavior

- The Responses API model should support a method for counting the input tokens that would be consumed by a given set of messages and tool definitions, by calling the appropriate OpenAI API endpoint.
- When running an agent with a usage limit that enforces input tokens ahead of time, and the model would exceed that limit, the run should stop early with a clear error message indicating the limit and the actual token count.
- When the projected token count is within the configured limit, the agent run should continue normally.
- Attempting to count tokens without providing any messages (or a reference to a previous server-side conversation) should result in a clear error.

## Why This Matters

Users who want to manage costs or avoid exceeding context windows proactively cannot do so with the OpenAI Responses model. They must wait for the actual inference request to fail rather than catching the issue before the request is made. Bringing the Responses model to parity with other supported providers would allow users to set meaningful input token budgets that are enforced consistently.
