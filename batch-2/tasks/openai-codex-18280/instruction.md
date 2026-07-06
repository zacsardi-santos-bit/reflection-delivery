I'm working on the TUI component of this project and I've found a bug in how session state is set up when reading a thread for replay or metadata inspection.

*   The ThreadSessionState struct must include a new optional field permission_profile of type Option<PermissionProfile>.

*   PermissionProfile must provide a constructor from_legacy_sandbox_policy that accepts a reference to a SandboxPolicy and a reference to a filesystem path (std::path::Path), returning a PermissionProfile instance.

*   When ThreadSessionState is constructed for an actively running session (not a thread/read operation), the permission_profile field must be populated by calling from_legacy_sandbox_policy with the session's sandbox policy and working directory.

*   The session_state_for_thread_read method must return a ThreadSessionState whose permission_profile field is None, regardless of whether a primary session with a non-None permission_profile exists. It must not copy or inherit the primary session's permission_profile into the read thread's session state.

*   The returned ThreadSessionState from session_state_for_thread_read must correctly reflect the read thread's own thread_id and cwd (working directory), not the primary session's values.


*   Interface details: Type: Struct Field
Name: permission_profile
Location: codex-rs/tui/src/app/ (ThreadSessionState struct)
Signature: permission_profile: Option<PermissionProfile>
Description: New optional field on ThreadSessionState that holds the runtime permission profile associated with the session. Must be None when the session state is created from a thread/read operation, and populated (Some) when built for an actively running session.

Type: Function (associated/constructor)
Name: from_legacy_sandbox_policy
Location: codex-rs/tui/src/ (on the PermissionProfile type)
Signature: PermissionProfile::from_legacy_sandbox_policy(sandbox_policy: &SandboxPolicy, cwd: &std::path::Path) -> PermissionProfile
Description: Constructs a PermissionProfile from a legacy SandboxPolicy and a working directory path. Used when building ThreadSessionState for actively running sessions.

Type: Method (async)
Name: session_state_for_thread_read
Location: codex-rs/tui/src/app/ (on the App struct or equivalent)
Signature: async fn session_state_for_thread_read(&mut self, thread_id: ThreadId, thread: &Thread) -> ThreadSessionState
Description: Builds a ThreadSessionState for a thread that is being read (metadata/replay hydration). The returned state must have permission_profile set to None — it must NOT inherit or reuse the primary session's permission_profile, even if one exists, because reusing it would apply cwd-bound permission entries against the wrong working directory.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.