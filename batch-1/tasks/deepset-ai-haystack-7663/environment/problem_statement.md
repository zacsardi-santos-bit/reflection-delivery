## Description

Haystack currently has a dynamic chat prompt builder component for building chat-style prompts inside pipelines, but it lacks a clean, general-purpose alternative that integrates naturally with the component system. We need a new unified chat prompt builder component that supports both static and dynamic (runtime-overridable) prompt templates made up of multiple chat messages.

## Expected Behavior

- Accepts a default list of chat messages at initialization, where each message can contain template placeholders
- Automatically exposes typed pipeline input sockets for the variables found in the template (user and system messages only)
- Supports an explicit list of variable names at construction time, overriding variable inference from the template
- Supports marking certain variables as required — if these are not provided at runtime, the component raises a descriptive error naming the missing variables
- Allows the template to be overridden entirely at runtime by passing a new list of messages
- At runtime, accepts both keyword arguments and a dedicated template-variables dict; the dict takes priority on collision
- Renders user and system messages through the templating engine; assistant messages are passed through unchanged
- Raises an error when no template is available (neither at init nor at runtime), when the template list is empty, or when the list contains non-message objects
- Raises a syntax error immediately — at initialization or at run time — when a message contains invalid template syntax

## Why This Matters

Developers building RAG and chat pipelines need a simple, first-class component to construct multi-turn prompts. The existing dynamic variant is cumbersome and is now superseded. This new component provides a single, unified solution that works for both static and fully dynamic use cases, making it easier to wire prompts from pipeline data.
