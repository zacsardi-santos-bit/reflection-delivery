I'm using the string tokenization function that accepts a custom list of separator strings.

*   When the `tokens` SQL function is invoked with the `splitByString` tokenizer and the separator array contains an empty string as any of its elements, the function must reject the query with a BAD_ARGUMENTS error.

*   This validation must apply regardless of the position of the empty string within the separator array — it should fail even if other separators in the array are valid non-empty strings (e.g., an array like [' ', ''] must be rejected just as [''] is rejected).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.