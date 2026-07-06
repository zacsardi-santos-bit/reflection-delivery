I'm working on a sandboxing system that wraps commands to restrict what they can access.

*   The tryRealpath function must be exported from packages/core/src/services/sandboxManager.ts and must accept a path string and return a Promise resolving to the canonical real path of that location.

*   When tryRealpath encounters a path that does not exist (ENOENT), it must fall back to resolving the parent directory and appending the original basename, recursively traversing up the directory tree until a real ancestor is found.

*   When tryRealpath reaches the root directory and still encounters ENOENT, it must return the original input path unchanged rather than throwing.

*   tryRealpath must re-throw any filesystem error that is not an ENOENT error (for example, EACCES permission errors must propagate to the caller).

*   The buildSeatbeltArgs function in packages/core/src/sandbox/macos/seatbeltArgsBuilder.ts must be changed from synchronous to asynchronous, returning Promise<string[]> instead of string[].

*   buildSeatbeltArgs must accept a forbiddenPaths optional field (string array) in its options object, and must use tryRealpath (from sandboxManager.ts) instead of fs.realpathSync for all path resolution.

*   When forbiddenPaths are provided to buildSeatbeltArgs, each path must be parameterized as FORBIDDEN_PATH_N and the profile must include a rule of the form: (deny file-read* file-write* (subpath (param "FORBIDDEN_PATH_N"))) for every forbidden path.

*   When a path appears in both allowedPaths and forbiddenPaths in buildSeatbeltArgs, the allow rule for that path must appear before the deny rule in the profile string so that the deny takes precedence (last-matching-rule semantics).

*   The sandbox policy object (used in SandboxRequest) must include a forbiddenPaths optional field (string array) alongside allowedPaths.

*   LinuxSandboxManager.prepareCommand must process policy.forbiddenPaths: non-existent paths produce --symlink /.forbidden <path> bwrap args; existing directories produce --tmpfs <path> --remount-ro <path>; existing files produce --ro-bind-try /dev/null <path>.

*   When a forbidden path on Linux is a symbolic link pointing to a file, LinuxSandboxManager must mask both the resolved real target path and the original symlink path, each with --ro-bind-try /dev/null.

*   When a forbidden path on Linux is a symbolic link pointing to a directory, LinuxSandboxManager must apply --tmpfs and --remount-ro to both the resolved real target and the original symlink path.

*   When a path appears in both allowedPaths and forbiddenPaths on Linux, LinuxSandboxManager must emit the allowed bind arguments first, then append the forbidden overlay arguments after.

*   WindowsSandboxManager.prepareCommand must process policy.forbiddenPaths: for each path that exists on disk, it must call icacls with arguments [path.resolve(forbiddenPath), '/deny', '*S-1-16-4096:(OI)(CI)(F)'] to deny Low Integrity access; non-existent forbidden paths must be silently skipped.

*   When a path appears in both allowedPaths and forbiddenPaths on Windows, the setintegritylevel (allow) icacls call must occur before the deny icacls call.

*   MacOsSandboxManager.prepareCommand must delegate seatbelt argument building entirely to buildSeatbeltArgs, passing workspace, allowedPaths, networkAccess, forbiddenPaths, workspaceWrite (false), and additionalPermissions ({ fileSystem: { read: [], write: [] }, network: true }).

*   The sandbox must block both read and write access to forbidden paths, even paths that fall within the workspace that would otherwise be accessible.

*   Forbidden paths that do not yet exist must be protected against creation (on Linux and macOS); non-existent forbidden paths must not cause sandbox setup to fail on any platform.

*   When a symbolic link is listed as a forbidden path, access through both the symlink path and the resolved real target must be blocked.


*   Interface details: Type: Function
Name: tryRealpath
Location: packages/core/src/services/sandboxManager.ts
Signature: tryRealpath(p: string): Promise<string>
Description: Async function that resolves a path to its real canonical path. On ENOENT, it falls back to resolving the parent directory and appending the original basename, recursively traversing up the directory tree if multiple ancestor directories are missing. Returns the original path unchanged when the root directory is reached and still encounters ENOENT. Re-throws non-ENOENT errors (e.g. EACCES). Must be exported from sandboxManager.ts.

Type: Function
Name: buildSeatbeltArgs
Location: packages/core/src/sandbox/macos/seatbeltArgsBuilder.ts
Signature: buildSeatbeltArgs(options: { workspace: string; allowedPaths?: string[]; networkAccess?: boolean; forbiddenPaths?: string[]; workspaceWrite?: boolean; additionalPermissions?: { fileSystem: { read: string[]; write: string[] }; network: boolean } }): Promise<string[]>
Description: Async function (changed from synchronous) that builds the argument array for macOS seatbelt (sandbox-exec). Accepts a new optional forbiddenPaths field. Uses tryRealpath (from sandboxManager.ts) instead of fs.realpathSync for path resolution. For each forbidden path, adds a FORBIDDEN_PATH_N parameter (e.g. -D FORBIDDEN_PATH_0=/resolved/path) and a deny rule of the form: (deny file-read* file-write* (subpath (param "FORBIDDEN_PATH_N"))) in the profile. When a path appears in both allowedPaths and forbiddenPaths, the allow rule must appear before the deny rule in the profile string so that the deny takes precedence (seatbelt last-matching-rule-wins semantics).

Type: Class
Name: LinuxSandboxManager
Location: packages/core/src/sandbox/linux/LinuxSandboxManager.ts
Description: The prepareCommand method must handle policy.forbiddenPaths. For each forbidden path, uses tryRealpath and fs.promises.stat to determine the path type. Non-existent forbidden paths (stat throws ENOENT) produce: --symlink /.forbidden <path>. Existing directories produce: --tmpfs <path> --remount-ro <path>. Existing files produce: --ro-bind-try /dev/null <path>. For forbidden symlinks to files: --ro-bind-try /dev/null <real-target>, --ro-bind-try /dev/null <original-symlink>. For forbidden symlinks to directories: --tmpfs <real-target>, --remount-ro <real-target>, --tmpfs <original-symlink>, --remount-ro <original-symlink>. When a path is in both allowedPaths and forbiddenPaths, the allowed bind args are emitted first, then the forbidden overlay args follow.

Type: Class
Name: WindowsSandboxManager
Location: packages/core/src/sandbox/windows/WindowsSandboxManager.ts
Description: The prepareCommand method must handle policy.forbiddenPaths. For each forbidden path that exists on disk, calls spawnAsync('icacls', [path.resolve(forbiddenPath), '/deny', '*S-1-16-4096:(OI)(CI)(F)']) to deny Low Integrity access. For non-existent forbidden paths, no icacls call is made (to prevent icacls failure on missing paths). When a path is in both allowedPaths and forbiddenPaths, the setintegritylevel (allow) icacls call must occur before the deny icacls call.

Type: Class
Name: MacOsSandboxManager
Location: packages/core/src/sandbox/macos/MacOsSandboxManager.ts
Description: The prepareCommand method must delegate seatbelt argument generation to buildSeatbeltArgs (from seatbeltArgsBuilder.ts), calling it with: { workspace, allowedPaths, networkAccess, forbiddenPaths, workspaceWrite: false, additionalPermissions: { fileSystem: { read: [], write: [] }, network: true } }. The final sandboxed command is: /usr/bin/sandbox-exec followed by the args returned by buildSeatbeltArgs, then -- <command> <args>.

Type: Interface/Type
Name: ExecutionPolicy (or SandboxPolicy)
Location: packages/core/src/services/sandboxManager.ts
Signature: policy.forbiddenPaths?: string[]
Description: The policy object accepted in SandboxRequest must include an optional forbiddenPaths field (array of path strings) alongside the existing allowedPaths field.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.