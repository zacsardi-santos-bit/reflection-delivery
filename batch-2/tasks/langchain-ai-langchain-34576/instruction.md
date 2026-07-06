Extend the conversation history summarization middleware to support complex trigger conditions using AND/OR logic. Implement validation to ensure configuration correctness and backward compatibility with existing single-condition triggers.

*   Implement the `SummarizationMiddleware` class with the following constructor and methods:
    *   `__init__(self, *, model, trigger, keep=..., token_counter=..., **kwargs)`
    *   `before_model(self, state, runtime) -> dict | None`
    *   `abefore_model(self, state, runtime) -> dict | None`
    *   `_should_summarize(self, messages: list, token_count: int) -> bool`

*   Ensure the `trigger` parameter accepts:
    *   A tuple for single OR conditions, e.g., `("tokens", 1000)`.
    *   A dict for AND conditions, e.g., `{"tokens": 1000, "messages": 5}`.
    *   A list for OR logic across items, supporting tuples and dicts, e.g., `[{"tokens": 5000, "messages": 3}, ("messages", 50)]`.
    *   An empty list `[]` that results in no summarization.

*   Validate the `trigger` parameter at construction time:
    *   Raise `ValueError` for unknown metric keys with message 'Unsupported trigger metric'.
    *   Raise `ValueError` for fraction values > 1 or <= 0 with message 'Fractional trigger values must be between 0 and 1'.
    *   Raise `ValueError` for `tokens` or `messages` values <= 0 with message 'trigger thresholds must be greater than 0'.
    *   Raise `ValueError` for non-numeric fraction values with message 'Fraction trigger values must be numeric'.
    *   Raise `ValueError` for float values for `tokens` or `messages` with message '{metric} trigger values must be integers'.
    *   Raise `ValueError` for string or boolean values for `messages` with message '{metric} trigger values must be integers' or 'messages trigger value must be numeric'.
    *   Raise `TypeError` for unsupported list items or top-level trigger types with messages 'Unsupported trigger item type' or 'Unsupported trigger type'.
    *   Raise `ValueError` for empty dicts `{}` with message 'at least one of'.

*   Ensure the `trigger` attribute is a deep copy of the constructor argument.

*   Implement `_should_summarize` to evaluate AND/OR logic, using provider-reported token usage for `tokens` and `fraction` metrics.

*   Ensure `before_model` and `abefore_model` return `None` when no older history is available, even if a trigger condition is met.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.