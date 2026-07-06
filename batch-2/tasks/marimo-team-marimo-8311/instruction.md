I'm working on the session caching system and need to introduce a distinction between read-write and read-only cache access.

*   A new CacheMode enum must be defined and exported from marimo/_session/extensions/extensions.py with at least two members: CacheMode.READ (read-only access) and CacheMode.READ_WRITE (full read and write access).

*   CachingExtension must accept a mode parameter of type CacheMode in its constructor (alongside the existing enabled parameter), and must expose that value as a publicly accessible mode attribute.

*   When CachingExtension is initialized with mode=CacheMode.READ and on_attach() is called with enabled=True, the cache writer must NOT be started (cache.start() must not be called), but the existing session view must still be loaded via cache.read_session_view().

*   When CachingExtension is in CacheMode.READ mode and on_session_notebook_renamed() is triggered, cache.rename_path() must NOT be called.

*   Sessions created in EDIT mode must have their CachingExtension initialized with mode=CacheMode.READ_WRITE. When auto_instantiate=False, enabled must be True; when auto_instantiate=True, enabled must be False.

*   Sessions created in RUN mode must have their CachingExtension initialized with mode=CacheMode.READ. When the runtime config option serve_cached_sessions_in_apps is True, enabled must be True; when it is False, enabled must be False.

*   A new configuration key serve_cached_sessions_in_apps must be supported under the runtime section of the application config, and its value must be respected when creating RUN mode sessions.


*   Interface details: Type: Enum
Name: CacheMode
Location: marimo/_session/extensions/extensions.py
Description: Enum that controls cache access mode for a session. Must have at least two members: READ (read-only cache access) and READ_WRITE (full read and write cache access).
Members: READ, READ_WRITE

Type: Class
Name: CachingExtension
Location: marimo/_session/extensions/extensions.py
Description: Extension that manages session-level caching. Must be updated to accept a mode parameter and expose it as a public attribute. In READ mode, on_attach() must skip starting the cache writer (cache.start() must not be called) but must still call cache.read_session_view(). In READ mode, on_session_notebook_renamed() must not call cache.rename_path().
Signature: __init__(self, enabled: bool, mode: CacheMode) -> None
Attributes: enabled (bool), mode (CacheMode)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.