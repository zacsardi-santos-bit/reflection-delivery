## Description

Apache Beam's machine learning inference framework supports several model providers, but it currently has no built-in support for Anthropic's Claude language models. Teams that want to run text through an Anthropic model as a step in a Beam pipeline must write all the API client setup, request formatting, and error-handling logic themselves.

## Expected Behavior

A new inference integration module should be added that:

- Allows a single-turn text prompt to be formatted as a user message and sent to the Anthropic API.
- Allows a multi-turn conversation history (a sequence of user and assistant messages) to be sent directly to the Anthropic API.
- Supports an optional system-level instruction that can be set once on the handler and applied to every request, with the ability to override it per request.
- Supports an optional structured output configuration (such as a JSON schema) that constrains the model's response format, also overridable per request.
- Automatically retries requests that fail due to transient server-side errors (such as rate limiting or temporary server unavailability), while not retrying requests that fail due to client errors.
- Plugs into the existing standard inference step in any Beam pipeline and returns results as standard prediction result objects with the original input and the model response accessible.
- Supports configuring minimum and maximum batch sizes for pipeline tuning.
- Creates the API client from an explicitly provided API key or from an environment variable when no key is passed.

## Why This Matters

Without this integration, every Beam user who wants to use Anthropic models must implement the same boilerplate for client setup, request serialization, and retry logic. A first-class integration removes this burden and makes Anthropic models as easy to use as any other supported provider in the framework.
