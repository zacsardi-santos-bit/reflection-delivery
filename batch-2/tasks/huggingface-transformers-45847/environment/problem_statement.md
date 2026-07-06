## Description

Several popular model families use chat templates that pre-fill the beginning of the assistant's turn in the prompt itself — for instance, by opening a reasoning block so the model starts generating inside it. The current response parsing system has no way to know what the prompt already wrote, so it only sees the model's raw tokens and mis-routes them into the wrong output section. This makes it impossible to reliably parse structured responses from models that use these kinds of prefilled prompts.

Beyond the prompt-prefix problem, the existing parser does not support streaming: callers must wait for the full response before they can extract any fields, which is inconvenient for real-time use cases.

## Expected Behavior

- A new declarative response parser should be provided that accepts the rendered prompt as a "prefix" so it can establish its correct initial state before processing model output.
- The parser should also support incremental streaming, emitting structured events (open, chunk, close) as tokens arrive.
- The parser should support a wide variety of model-specific output formats through a flexible template configuration, including literal and regex delimiters, repeating fields, optional fields, multiple content types (plain text, integers, JSON, and structured key-value formats), and value transformations.
- Text-generation pipelines and multi-modal chat pipelines should pass the rendered prompt prefix to the parser when the chat template pre-fills the assistant turn, so the final structured output is correct.
- The template configuration must be saveable and reloadable alongside a tokenizer.

## Why This Matters

Without the prefix-aware parser, applications that use chain-of-thought or reasoning models — where the chat template opens a thinking block in the prompt — will silently misparse responses, placing the model's reasoning in the wrong output field or dropping it entirely. This causes downstream failures in tool-call detection, thinking extraction, and any other logic that relies on structured response fields.
