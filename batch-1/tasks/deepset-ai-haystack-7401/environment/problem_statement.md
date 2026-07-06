## Description

Haystack needs a built-in evaluator component that uses a language model to assess the quality of pipeline outputs. Currently, developers who want LLM-based evaluation (e.g., checking responses for toxicity, factual accuracy, or relevance) have to manually build and wire up their own evaluation pipelines. There is no reusable, declarative component for this purpose.

## Expected Behavior

A new evaluator component should allow developers to:

- Define evaluation criteria in natural language (the "instructions")
- Specify which inputs the evaluator receives and what output scores it should produce
- Provide few-shot examples showing expected inputs and the corresponding judgments
- Optionally configure the underlying LLM API and authentication

The component should validate its configuration at initialization time, rejecting malformed inputs, outputs, or examples with clear error messages. It should also validate at runtime that all input lists have the same length.

The component should be fully serializable and deserializable so it can be saved and restored as part of a pipeline. It should produce structured, consistent output — a list of result dictionaries, one per evaluated item — that downstream components can consume reliably.

The component should support being imported directly from the main evaluators package.

## Why This Matters

LLM-as-judge evaluation is a standard quality-assurance technique for AI pipelines. Without a built-in component, developers must reinvent this plumbing repeatedly. A well-validated, serializable evaluator component makes it straightforward to add quality checks as a first-class step in any Haystack pipeline.
