## Description

The gRPC service generation tool currently has no schema validation step. When developers provide a GraphQL schema that uses patterns the tool doesn't support — like nested key references or nullable items in lists — the generator either produces incorrect output silently or fails in a confusing way much later in the pipeline. There is no structured feedback distinguishing between non-critical issues that can be tolerated and critical issues that prevent correct code generation.

## Expected Behavior

- Before generating any output files, the tool should validate the provided GraphQL schema and classify any issues as either warnings or errors.
- Issues that indicate unsupported but non-fatal features (e.g., nullable items in a list, or use of unsupported directives that are not yet implemented) should be reported as warnings. Generation should continue and produce the expected output files.
- Issues that would prevent correct code generation (e.g., nested key references that cannot be properly mapped) should be reported as errors. Generation must stop immediately and no output files should be created.
- When both warnings and errors are present, errors take precedence: generation must still be halted.

## Why This Matters

Developers currently receive no actionable feedback about schema compatibility. They either get a cryptic failure deep in the pipeline, or they silently receive broken output. A clear, early validation step with a proper distinction between warnings and errors makes the tool significantly more useful and developer-friendly.
