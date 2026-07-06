## Description

When running evaluations in CI or other automated environments, it's useful to attach run-specific metadata — like the environment name or a build identifier — directly from the command line, without having to edit the shared configuration file. Currently, there's no way to pass such tags at runtime; the only option is to hardcode them in the config, which makes it impossible to vary them per run.

## Expected Behavior

- Users should be able to pass one or more key-value tags when invoking an evaluation from the command line. Multiple tags should be accepted in a single run.
- Runtime tags specified on the command line should be merged with any tags already present in the project configuration. When the same key exists in both, the command-line tag should take precedence.
- The project configuration should also support specifying tag defaults that apply before any explicitly provided runtime tags. Runtime tags override these defaults when keys collide.
- Attempting to apply runtime tags while resuming or retrying a previous evaluation should be rejected with a clear error message, since those operations need to preserve the original tags.
- The merged tags should be reflected in both the saved evaluation record and the test suite that is executed.

## Why This Matters

Without this feature, teams running evaluations across multiple environments or CI pipelines must maintain separate config files or resort to post-processing to tag results correctly. Supporting runtime tags makes it easy to annotate each run with the context it was executed in, enabling better filtering and tracking of results without touching shared configuration.
