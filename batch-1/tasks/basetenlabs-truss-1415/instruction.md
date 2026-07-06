Implement a refactor of the model and chain deployment APIs to automatically collect and send client environment metadata, removing the need for callers to provide a client version and trust flag. Transition all GraphQL API requests to use JSON bodies and update relevant methods and data structures accordingly.

*   Update GraphQL API requests:
    *   Use JSON body format for all POST requests, with `json['query']` for the query string and `json['variables']` for variables.
    *   Include a variables dictionary `{'trussUserEnv': TrussUserEnv.collect().model_dump_json()}` in the JSON body for every GraphQL mutation.

*   Modify API method signatures:
    *   Remove `client_version` and `is_trusted` parameters from `create_model_version_from_truss`, `create_model_from_truss`, and `create_development_model_from_truss` methods in `truss/remote/baseten/api.py`.
    *   Remove `is_trusted` parameter from `create_truss_service` in `truss/remote/baseten/core.py`.
    *   Remove `trusted` parameter from the `push` method in `truss/remote/baseten/remote.py`.

*   Update data structures:
    *   Remove the `is_trusted` field from the `ChainletDataAtomic` class in `truss/remote/baseten/custom_types.py`.

*   Refactor chain deployment GraphQL mutations:
    *   Use parameterized variable declaration `mutation ($trussUserEnv: String)` and pass `truss_user_env: $trussUserEnv` to `deploy_chain_atomic`.
    *   Remove inlining of `client_version` as a string literal in mutation bodies.

*   Implement the `TrussUserEnv` class:
    *   Create a Pydantic model in `truss/remote/baseten/custom_types.py` with a `collect()` classmethod returning an instance of `TrussUserEnv`.
    *   Ensure `TrussUserEnv` can serialize to JSON using `model_dump_json()`.

*   Adjust Python version resolution:
    *   Rename `map_to_supported_python_version` to `_map_to_supported_python_version` and make it private in `truss/base/truss_config.py`.
    *   Implement mapping for supported versions: 'py38'→'py38', 'py39'→'py39', 'py310'→'py310', 'py311'→'py311', 'py312'→'py311'.
    *   Raise `ValueError` for unsupported minor versions with a message: "Mapping python version 3.{minor} to 3.8, the lowest version that Truss currently supports."
    *   Raise `NotImplementedError` for unsupported major versions with a message: "Only python version 3 is supported".

*   Remove legacy plugin configuration:
    *   Eliminate the `gemm_plugin` field from the TrTLLM plugin configuration, ensuring configurations without it parse and round-trip correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.