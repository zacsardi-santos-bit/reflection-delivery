## Description

When a user specifies the model selection flag more than once on the command line, the argument parser produces an array of values instead of a single string. The model resolution and model classification logic currently assumes it always receives a single string, which causes crashes or incorrect behavior when it receives an array or another non-string value at runtime.

## Expected Behavior

- When the model flag is specified multiple times, the system should use the last specified value (last wins), consistent with how most command-line tools handle repeated flags.
- When a non-string value is passed to model resolution functions at runtime, the value should be safely coerced to its string representation rather than causing an error.
- The model classification function (which checks whether a model is a custom/non-standard model) should also handle array inputs without throwing, applying the same last-wins logic.

## Why This Matters

Users who accidentally specify the model flag more than once — or tooling/scripts that pass model values in unexpected formats — currently encounter crashes or unpredictable behavior. Making model resolution robust to these cases provides a better user experience and prevents confusing errors that are unrelated to the user's actual intent.
