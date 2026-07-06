## Description

When a model that expects named (dictionary) inputs receives the wrong type of input, the error message it shows is too generic and doesn't tell the user what named keys are expected. Similarly, models expecting a specific number of positional inputs could give more descriptive error messages.

## Expected Behavior

- When a model built with named/dictionary inputs receives the wrong kind of input, the error message should clearly indicate that named inputs with specific keys are expected, helping the user understand they need to pass a dictionary.
- When a model expecting a specific number of positional inputs receives the wrong count, the error message should be more descriptive about what kind of inputs are expected (not just a bare count).

## Why This Matters

Better error messages reduce debugging time significantly. A developer who accidentally passes a single tensor to a model expecting a dictionary of named tensors should immediately understand from the error what they need to fix, rather than having to dig through documentation to understand the input format.
