Implement the ability for the CircleCI CLI's policy evaluation commands to compile configuration files through the CircleCI API before policy evaluation. Ensure that users can opt out of this compilation if desired.

*   Modify the `decide` and `eval` sub-commands:
    *   Accept a `--no-compile` boolean flag.
    *   By default (when `--no-compile` is not set), compile the input config using the CircleCI API.
    *   Append the compiled config under a top-level `_compiled_` YAML key alongside the source config.
    *   If the source config already contains a `_compiled_` key, remove it from the compiled output before merging.

*   Handle command flags and errors:
    *   Require the `--owner-id` flag when compilation is enabled. If missing, output the error: `--owner-id is required for compiling config (use --no-compile to evaluate policy against source config only)`.
    *   Remove the restriction preventing the use of a local policy file path with `--owner-id`.

*   Update the `makeCMD` function:
    *   Accept a `circleHost string` parameter to initialize `settings.Config` with `Host: circleHost`.

*   Modify the `config` package:
    *   Add a `ConfigYaml string` field to the `CompileConfigRequest` struct for JSON-decoding the request body.
    *   Ensure the `ConfigResponse` struct includes `Valid bool`, `SourceYaml string`, and `OutputYaml string` fields.
    *   Change the `ProcessConfig` method to return `(*ConfigResponse, error)`.

*   Add a new `--owner-id` flag to the `eval` command.

*   Ensure the `NewCommand` function uses `settings.Config.Host` as the base URL for the config compilation service.

*   Include a test YAML file at `cmd/policy/testdata/test4/config.yml` with a `_compiled_` top-level key and a regular key for testing.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.