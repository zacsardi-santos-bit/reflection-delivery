Implement a new linting rule in the modeling structure linter to ensure that when a model class declares tied weights, the corresponding configuration class includes a flag to control this behavior. This rule should detect and report any violations where the flag is missing, ensuring that users can disable weight tying if needed.

*   Define a constant `TRF015` in the `mlinter` module as a string rule ID for the new lint rule.
    *   Ensure it can be used in `enabled_rules` sets for `analyze_file` and `_content_hash`, and compared against `Violation.rule_id`.
*   Implement the `check` function in `utils/mlinter/trf015.py`:
    *   Accept `tree`, `file_path`, and `source_lines` as parameters.
    *   For each `PreTrainedModel` subclass with a non-empty `_tied_weights_keys`, find the corresponding configuration file and resolve the target config class.
    *   Produce a `Violation` if the config class lacks `tie_word_embeddings`.
    *   Ensure `Violation.rule_id` equals `RULE_ID` and `Violation.message` includes "tie_word_embeddings" and the offending config class name.
*   Ensure no TRF015 violation is produced if:
    *   `_tied_weights_keys` is an empty collection.
    *   The target config class inherits from a base class other than `PreTrainedConfig`/`PretrainedConfig` with a name ending in "Config".
*   Match config files by suffix: 
    *   `modeling_foo_text.py` should match `configuration_foo_text.py` before any other `configuration_*.py`.
*   Resolve config classes in this order:
    *   Explicit `config_class = SomeConfig` assignment.
    *   `config: SomeConfig` type annotation.
    *   Longest shared prefix between model class and config class names.
*   Ensure a main composite config class directly inheriting from `PreTrainedConfig` declares `tie_word_embeddings` itself.
*   Implement `_find_companion_files` in `utils/mlinter/mlinter.py`:
    *   Return a list of companion configuration files for `modeling_*.py` and `modular_*.py` files.
*   Implement `_content_hash` in `utils/mlinter/mlinter.py`:
    *   Accept `text`, `enabled_rules`, and `companion_files`.
    *   Incorporate companion file contents into the hash to ensure cache invalidation when config files change.
*   Update `rules.toml` with a `[rules.TRF015]` section:
    *   Include a description, set `default_enabled = true`, and `allowlist_models = []`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.