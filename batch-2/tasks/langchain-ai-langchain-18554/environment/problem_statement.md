## Description

The official Anthropic partner integration for LangChain is missing an experimental module for tool-calling and structured output extraction with Claude models. Users who want to bind schemas to Claude and extract structured, typed data currently have to rely on a deprecated community-maintained module. This capability should be provided natively in the official partner package.

## Expected Behavior

- A new experimental module should be added to the official Anthropic partner package that exposes a chat model class with tool-calling support.
- The new class should support all standard chat model operations: synchronous and asynchronous invocation, streaming, and batching — returning string content.
- The class must also work correctly when used in prompt chains that include system messages.
- The class must support binding data schemas as tools, enabling structured data extraction from natural language inputs.
- When structured output is requested, invoking the chain with natural language should yield a correctly typed and populated schema object instance with the right field values.

## Why This Matters

Developers building applications with Anthropic's Claude models through LangChain should have a first-party, well-maintained way to extract structured data. Relying on a deprecated and unmaintained community module is fragile. Moving this capability into the official partner package ensures it stays up to date and is properly integrated with the rest of the LangChain ecosystem.
