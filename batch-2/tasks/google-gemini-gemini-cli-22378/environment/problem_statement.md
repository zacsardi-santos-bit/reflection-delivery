## Description

The Gemini CLI rejects certain valid model identifiers when users try to specify them via the model selection option on the command line. The current Flash preview model has an identifier that includes "preview" as part of its name, but the CLI's internal model validation does not recognize this identifier. When users pass this updated identifier on the command line, the CLI throws an error claiming the model is invalid, even though it is a legitimate and currently available model.

## Expected Behavior

- The model selection option should accept the current Flash preview model identifier (which includes "preview" in the name) without errors
- When a model is explicitly specified via the command-line model option, it should be used as provided and take priority over any model configured in settings

## Why This Matters

Users who want to use the latest preview models are blocked from doing so. The hardcoded allowlist of valid model names does not keep pace with model naming updates, causing the validation to be both unhelpful and misleading. The validation provides no real safety benefit while actively preventing users from accessing valid models.
