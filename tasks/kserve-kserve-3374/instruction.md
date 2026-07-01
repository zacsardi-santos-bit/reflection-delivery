Implement a compatibility feature in the data models module to support two major versions of a data validation library. Create a boolean flag to indicate which version is installed and adjust the server's health check response format accordingly.

*   Define a module-level boolean variable named `is_pydantic_2` in `python/kserve/kserve/protocol/rest/v2_datamodels.py`.
    *   Ensure `is_pydantic_2` is importable from `kserve.protocol.rest.v2_datamodels`.
    *   Set `is_pydantic_2` to `True` if pydantic version 2.x is installed.
    *   Set `is_pydantic_2` to `False` if pydantic version 1.x is installed.

*   Modify the model health check endpoint to adjust the serialization of the `ready` field based on the `is_pydantic_2` flag.
    *   If `is_pydantic_2` is `True`, serialize the `ready` field as a native JSON boolean (e.g., `true`).
        *   Example response: `{"name":"TestModel","ready":true}`
    *   If `is_pydantic_2` is `False`, serialize the `ready` field as a Python string (e.g., `"True"`).
        *   Example response: `{"name":"TestModel","ready":"True"}`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.