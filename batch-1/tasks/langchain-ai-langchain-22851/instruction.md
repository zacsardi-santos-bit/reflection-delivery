Implement a feature in the ChatPromptTemplate to correctly generate an input schema that includes a 'required' field listing all mandatory input variables. Ensure that validation errors are raised when required fields are missing.

*   Update the ChatPromptTemplate to include a 'required' key in its input schema.
    *   The 'required' key must list all non-optional input variable names.
*   Ensure that a MessagesPlaceholder created with optional=False has its variable name included in the 'required' list of the input schema.
*   Ensure that a MessagesPlaceholder created with optional=True does not have its variable name included in the 'required' list.
*   Implement input schema validation to raise a ValidationError when a required field is absent.
*   Ensure that validation succeeds without errors when only optional fields are absent and all required fields are provided.
*   Include regular string template variables in the 'required' list, as they are always mandatory.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.