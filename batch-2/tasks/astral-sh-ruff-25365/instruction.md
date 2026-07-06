I'm working on improving the robustness of the language server's request handling.

*   When a code action resolve request is received for a code action that has no data field, the server must return the original code action unchanged.

*   When a code action resolve request is received for a code action whose data field contains a value that cannot be parsed as a document URL (e.g., a plain string), the server must return the original code action unchanged.

*   When a hover request is made for a document that is not currently open in the server, the server must return a null/empty response (None).

*   When a document diagnostic request is made for a document that is not currently open in the server, the server must return a full diagnostic report with an empty items list, represented as JSON: {"kind": "full", "items": []}.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.