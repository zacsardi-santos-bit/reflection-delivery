I'm working on the CI pre-commit hook that checks breeze command configuration.

*   breeze_env_with_local_sources() must return a dict[str, str] containing all environment variables from the current process environment, with PYTHONPATH modified as described below.

*   When PYTHONPATH is not set in the environment, the returned dict must have PYTHONPATH set to exactly str(BREEZE_SOURCES_DIR).

*   When PYTHONPATH is already set to an existing value, the returned dict must have PYTHONPATH set to f"{BREEZE_SOURCES_DIR}{os.pathsep}{existing_pythonpath}", ensuring BREEZE_SOURCES_DIR is the first element when the resulting string is split by os.pathsep.

*   Calling breeze_env_with_local_sources() must not modify the live process environment (os.environ); after the call, PYTHONPATH must remain absent from os.environ if it was absent before the call.

*   All environment variables other than PYTHONPATH that are present in os.environ must be preserved unchanged in the returned dict.


*   Interface details: Type: Constant
Name: BREEZE_SOURCES_DIR
Location: scripts/ci/prek/breeze_cmd_line.py
Description: A path-like constant pointing to the local breeze sources directory. Must be convertible to a string via str(). Already defined in the existing file; must remain exported.

Type: Function
Name: breeze_env_with_local_sources
Location: scripts/ci/prek/breeze_cmd_line.py
Signature: breeze_env_with_local_sources() -> dict[str, str]
Description: Returns a copy of the current process environment (os.environ) in which PYTHONPATH is set to str(BREEZE_SOURCES_DIR) when previously unset, or prepended as f"{BREEZE_SOURCES_DIR}{os.pathsep}{existing_PYTHONPATH}" when already set. Must not mutate os.environ. All other environment variables must be preserved unchanged.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.