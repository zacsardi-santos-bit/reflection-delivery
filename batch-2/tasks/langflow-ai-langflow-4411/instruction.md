Implement clear and consistent error messages for data-handling components. Ensure the error messages for exceeding field limits and invalid text keys are correctly formatted and easy to read.

*   In the `CreateDataComponent` class:
    *   Update the `update_build_config` method to raise a `ValueError` when the `number_of_fields` exceeds 15.
        *   The error message must include the substring: 'Number of fields cannot exceed 15.'

*   In the `UpdateDataComponent` class:
    *   Update the `update_build_config` method to raise a `ValueError` when the `number_of_fields` exceeds 15.
        *   The error message must include the substring: 'Number of fields cannot exceed 15.'
    *   Update the `validate_text_key` method to raise a `ValueError` if `self.text_key` is set but not found in `data.data.keys()`.
        *   The error message must be formatted as: "Text Key: '{text_key}' not found in the Data keys: {', '.join(data.data.keys())}"
            *   Ensure single quotes around the `text_key`.
            *   Ensure a comma followed by a space between each listed data key.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.