Implement improved error handling for SQL templates in Superset by updating the error response codes and messages, and creating a utility for template validation. Ensure that user-caused syntax errors return HTTP 422 with descriptive messages, and server-side errors return HTTP 500. Provide a standalone utility for validating Jinja2 template syntax.

*   Update `process_template` method in `BaseTemplateProcessor`:
    *   Catch `TemplateSyntaxError`, `SecurityError`, `UndefinedError`, `UnicodeError`, `UnicodeDecodeError`, and `UnicodeEncodeError`.
        *   Raise `SupersetSyntaxErrorException` with:
            *   Status 422.
            *   A single `SupersetError` with message format: "Jinja2 template error ({exception_type}): {error_msg}".
            *   `error_type` set to `SupersetErrorType.GENERIC_COMMAND_ERROR`.
            *   `extra` dictionary containing a "template" key with the first 500 characters of the template string.
    *   Catch all other unexpected exceptions (e.g., `MemoryError`).
        *   Raise `SupersetTemplateException` with message: "Internal Jinja2 template error ({exception_type}): {error_msg}".
*   Modify `SupersetTemplateException` in `superset/exceptions.py`:
    *   Ensure `status` is set to 422.
*   Define `SupersetSyntaxErrorException` in `superset/exceptions.py`:
    *   Include an `errors` attribute (list of `SupersetError` objects).
    *   Set `status` to 422.
*   Adjust dataset API endpoint for rendered SQL:
    *   Return HTTP 422 when template rendering fails for dataset queries, metrics, or column expressions.
    *   Include a 'message' field in the response body with existing error text.
*   Create `superset/utils/jinja_template_validator.py` module:
    *   Define `JinjaValidationError` class:
        *   Include a `message` attribute (string) for error descriptions.
    *   Implement `validate_jinja_template(template_str: str) -> None`:
        *   Pass silently for valid or empty templates.
        *   Raise `JinjaValidationError` with message "Invalid Jinja2 template syntax" for invalid templates.
    *   Implement `validate_params_json_with_jinja(value: str | None) -> None`:
        *   Accept `None` silently.
        *   Raise `marshmallow.ValidationError` with "Invalid JSON" for malformed JSON.
        *   Raise `marshmallow.ValidationError` with "Invalid Jinja2 template" for valid JSON containing invalid Jinja2 in `adhoc_filters` entries' `sqlExpression` fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.