Update the `ensure_config` function in `libs/core/langchain_core/runnables/config.py` to promote the 'checkpoint_ns' field into the metadata dictionary, similar to how the 'model' field is handled. Ensure that both fields are properly managed in the configuration process.

*   Implement the `ensure_config` function with the following behavior:
    *   When called with a config whose `configurable` dictionary contains a 'checkpoint_ns' key with a string value:
        *   Copy the 'checkpoint_ns' value into `config['metadata']` if it is not already present.
    *   When the `configurable` dictionary contains both 'model' and 'checkpoint_ns' as string values:
        *   Ensure both 'model' and 'checkpoint_ns' are present in `config['metadata']` if they are not already there.
    *   When a top-level 'checkpoint_ns' key is present in the input dictionary:
        *   Move 'checkpoint_ns' into `config['configurable']`.
        *   Copy 'checkpoint_ns' into `config['metadata']`.
    *   Do not override 'checkpoint_ns' in `config['metadata']` if it is already present, maintaining consistency with the 'model' field behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.