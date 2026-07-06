Implement a module to manage update result files in JSON format for the TinyPilot application. Define a `Result` dataclass to represent the outcome of update operations, and create `read()` and `write()` functions to handle JSON serialization and deserialization. Ensure timestamps are managed in ISO-8601 format.

*   Create a module named `update_result` in `app/update_result.py`.
    *   Define a `Result` dataclass with:
        *   `success`: a boolean indicating the success of the update.
        *   `error`: a string containing any error message.
        *   `timestamp`: a `datetime.datetime` object representing when the update occurred.
    *   Implement the `read(result_file) -> Result` function:
        *   Accept a file object containing JSON data.
        *   Return a `Result` object with fields populated from the JSON.
        *   Parse the `timestamp` field as an ISO-8601 string using the format 'YYYY-MM-DDTHHMMSSZ'.
        *   Use default values for missing fields: `success=False`, `error=''`, `timestamp` set to Unix epoch.
    *   Implement the `write(result: Result, result_file) -> None` function:
        *   Accept a `Result` object and a file object.
        *   Serialize the `Result` object to a single-line JSON string.
        *   Format the `timestamp` as an ISO-8601 string.
        *   Ensure the JSON contains unquoted boolean values for `success`.

*   Utilize the `iso8601` utility module in `app/iso8601.py`:
    *   Use `to_string(dt: datetime.datetime) -> str` to convert `datetime` objects to ISO-8601 strings.
    *   Use `parse(iso_8601_string: str) -> datetime.datetime` to convert ISO-8601 strings to `datetime` objects.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.