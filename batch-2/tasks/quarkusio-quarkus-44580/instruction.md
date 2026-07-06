Implement a fix for the reflection-free JSON serializer optimization in your Quarkus REST application to ensure correct serialization of fields with names that match or start with Java accessor prefixes. This will prevent incorrect JSON output when such fields are present.

*   Ensure that fields named exactly 'set', 'get', or 'is' are serialized with those exact names as keys in JSON output.
*   Ensure that fields whose names start with 'set', 'get', or 'is' but have additional characters (e.g., 'setText') are serialized with their full names as keys in JSON output.
*   Implement a GET endpoint at '/field-name-prefixes' that:
    *   Returns a record with fields: id (String), set (boolean), get (boolean), is (boolean), and setText (String).
    *   Responds with HTTP status 200.
    *   Has a content-type of application/json.
    *   Includes a JSON body with keys matching the exact field names and their corresponding values.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.