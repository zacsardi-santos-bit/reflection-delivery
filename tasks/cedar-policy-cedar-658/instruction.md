Fix the Cedar policy schema serializer to correctly handle actions with no applies-to specification. Ensure that these actions are represented in the natural schema format without any unnecessary applies-to clauses.

*   Ensure the conversion of a schema fragment with an action lacking an applies-to specification succeeds without error.
*   Format the natural schema output for such actions to include only the action declaration:
    *   Use the format: `action "actionName" ;` where `actionName` is the quoted name of the action.
    *   Do not include any applies-to clauses or blocks.
*   Prevent the emission of any applies-to block in the natural schema output for actions without applies-to information:
    *   Avoid output like `appliesTo { context : {} }` when the applies-to field is absent or None in the source action.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.