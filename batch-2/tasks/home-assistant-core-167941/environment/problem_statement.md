# Anthropic Integration: Validate Thinking Budget Against Max Tokens

## Description

When configuring the Anthropic integration's thinking budget feature, there is currently no validation to prevent users from setting a thinking budget that is equal to or larger than the maximum token limit. This would result in an invalid configuration where the AI has no tokens left to produce an actual response after spending its entire budget on reasoning.

Additionally, the maximum token limit is currently shown in the general model settings step, even though it is closely related to the thinking budget setting which appears later in the model-specific options step. This separation makes it impossible to validate the relationship between these two values at the time of input.

## Expected Behavior

- The maximum token limit should be moved to the model-specific settings step, alongside the thinking budget and other model-specific options.
- When a user provides a thinking budget that is greater than or equal to the maximum token limit, the configuration form should reject the input and display a validation error on the thinking budget field.
- When a valid combination is provided (thinking budget less than max tokens), the configuration should save and complete successfully.
- The model-specific settings step should always be shown, since it now always contains the maximum token limit.

## Why This Matters

Users can currently save an invalid configuration where the thinking budget consumes all available tokens, leaving no room for the actual response. By moving these related settings together and adding proper validation, users get immediate feedback when their configuration is logically inconsistent — preventing silent failures at runtime.
