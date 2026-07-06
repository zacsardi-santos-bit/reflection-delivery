Implement a class `SummarizationMiddleware` in the specified location to manage trigger conditions for summarization. Ensure it maintains two separate internal representations of triggers: a canonical representation for all triggers, including compound ones, and a legacy view for simple single-condition triggers.

*   Define the class `SummarizationMiddleware` in `libs/langchain_v1/langchain/agents/middleware/summarization.py`.
*   During initialization, set the following attributes:
    *   `_trigger_clauses`: 
        *   Store a list of dictionaries representing all trigger conditions.
        *   Convert input tuples like `('messages', 5)` to `{'messages': 5}`.
        *   Include input dictionaries as-is, whether single-key or multi-key.
        *   Wrap a single dictionary input in a list.
    *   `_trigger_conditions`: 
        *   Store a list of tuples representing simple single-condition triggers.
        *   Include only triggers expressible as single (key, value) tuples.
        *   Convert single-key dictionaries to tuples.
        *   Exclude multi-key compound dictionary clauses entirely.

*   Ensure the following behavior:
    *   For a mixed trigger list like `[('messages', 5), {'tokens': 1000}, {'tokens': 2000, 'messages': 10}]`, `_trigger_clauses` must be `[{'messages': 5}, {'tokens': 1000}, {'tokens': 2000, 'messages': 10}]` and `_trigger_conditions` must be `[('messages', 5), ('tokens', 1000)]`.
    *   For a single compound trigger dict like `{'tokens': 1000, 'messages': 5}`, `_trigger_clauses` must be `[{'tokens': 1000, 'messages': 5}]` and `_trigger_conditions` must be `[]`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.