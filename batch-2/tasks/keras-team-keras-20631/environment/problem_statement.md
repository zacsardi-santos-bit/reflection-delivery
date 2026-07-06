## Description

The current model export API has a few shortcomings that make it harder to use than it should be:

1. The main export function has a vague name that doesn't communicate the output format being produced. It should be renamed to make the target format explicit.
2. There is no way to specify the input signature (shape and dtype information) when exporting a model, which is needed for certain deployment scenarios.
3. There's no convenient method on the model object itself to trigger export — users must go through a separate library import.
4. When export is attempted on unsupported backends or with unsupported format names, the errors produced are not clear or actionable.

## Expected Behavior

- The export function in the export library should have a name that clearly reflects the output format it produces.
- The export function should accept an optional input signature argument, supporting various input specification types including shape-and-type descriptor objects and actual tensor values.
- Passing an unsupported type as an input signature element should raise a clear type error indicating which value is not supported.
- Model objects should have an export method accepting a filepath and optional format parameter.
- Calling export with an unrecognized format name should raise a clear error identifying the bad format value.
- Calling export on a backend that does not support the export feature should raise a descriptive error explaining the limitation.
- For users of certain backends, the export function should accept backend-specific options (a static execution flag and conversion keyword arguments) to control how the model is lowered.

## Why This Matters

A clearer export API reduces confusion about what format is being produced and makes export accessible directly from model objects, without requiring extra imports. Good error messages for unsupported configurations save developers debugging time.
