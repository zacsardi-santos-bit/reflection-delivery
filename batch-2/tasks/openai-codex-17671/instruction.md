I'm working on the integration test infrastructure for a Rust project that tests file system operations in both local and remote containerized environments.

*   The new test support library must export a public struct called TestBinaryDispatchGuard with a paths() method that returns a reference to Arg0DispatchPaths.

*   The new test support library must export a public enum called TestBinaryDispatchMode with exactly three variants: DispatchArg0Only, Skip, and InstallAliases.

*   The new test support library must export a public function called configure_test_binary_dispatch that accepts a codex_home_prefix string and a classify closure (taking an executable name str and an optional argv1 str, returning TestBinaryDispatchMode), and returns Option<TestBinaryDispatchGuard>. When the classify closure returns DispatchArg0Only, the function dispatches via arg0 and returns None. When it returns Skip, the function returns None. When it returns InstallAliases, it sets up a temporary CODEX_HOME, installs arg0 aliases, restores the previous CODEX_HOME, and returns Some(TestBinaryDispatchGuard).

*   The new test support library must be a standalone Rust crate named codex-test-binary-support located at codex-rs/test-binary-support/lib.rs, with dependencies on codex-arg0 and tempfile.

*   The remote exec server setup must copy the codex-linux-sandbox binary (resolved by name 'codex-linux-sandbox') to the remote container at a path derived from the instance ID (e.g. /tmp/codex-linux-sandbox-{instance_id}), make it executable, probe it before starting the server, and add its remote path to cleanup_paths.

*   A probe_remote_linux_sandbox function must accept a container name and a remote linux sandbox path, serialize a read-only sandbox policy to JSON, run the sandbox binary in the container with that policy and cwd /tmp using the command pattern '{remote_linux_sandbox_path} --sandbox-policy-cwd /tmp --sandbox-policy {policy} -- /bin/true', and return an error (including stdout and stderr in the message) if the command fails.

*   Remote integration tests must verify that reading a file through a sandbox that includes its parent directory as a readable root succeeds and returns the correct file contents.

*   Remote integration tests must verify that attempting to read a file through a path that uses a symlink to an outside directory followed by '..' to escape the sandbox is rejected with an error whose kind is NotFound, InvalidInput, or PermissionDenied, and whose message contains one of: 'No such file or directory', 'is not permitted', 'Operation not permitted', or 'Permission denied'.

*   Remote integration tests must verify that removing a symlink within a sandboxed workspace removes only the symlink (it no longer exists) while the file the symlink pointed to outside the sandbox still exists with its original contents.

*   Remote integration tests must verify that copying a symlink within a sandboxed workspace produces a new symlink (not a regular file) that points to the same target path as the original.

*   The exec-server test helper must provide a public struct TestCodexHelperPaths with public fields codex_exe: PathBuf and codex_linux_sandbox_exe: Option<PathBuf>, and a function test_codex_helper_paths() returning anyhow::Result<TestCodexHelperPaths>.

*   The exec-server tests for sandboxed read (allowing readable root), symlink parent dotdot escape rejection, symlink removal, and symlink copy must run only in local mode (not in a remote parameterized mode); their remote equivalents are separately covered in the remote environment test suite.


*   Interface details: Type: Crate
Name: codex-test-binary-support
Location: codex-rs/test-binary-support/lib.rs
Description: New shared library crate that centralizes test binary dispatch setup logic. Must declare the crate name as codex_test_binary_support. Dependencies: codex-arg0, tempfile.

Type: Struct
Name: TestBinaryDispatchGuard
Location: codex-rs/test-binary-support/lib.rs
Description: Public struct returned by configure_test_binary_dispatch when aliases are installed. Holds a temporary CODEX_HOME directory and the arg0 path entry guard. Must have a public paths() method.
Signature: pub fn paths(&self) -> &Arg0DispatchPaths

Type: Enum
Name: TestBinaryDispatchMode
Location: codex-rs/test-binary-support/lib.rs
Description: Public enum returned by the classify closure passed to configure_test_binary_dispatch. Controls what the function does for the current process invocation.
Variants:
  - DispatchArg0Only  — dispatch via arg0 and return None (no aliases installed)
  - Skip              — do nothing, return None
  - InstallAliases    — set up a temporary CODEX_HOME, install arg0 dispatch aliases, restore the previous CODEX_HOME, return Some(TestBinaryDispatchGuard)

Type: Function
Name: configure_test_binary_dispatch
Location: codex-rs/test-binary-support/lib.rs
Signature: pub fn configure_test_binary_dispatch<F>(codex_home_prefix: &str, classify: F) -> Option<TestBinaryDispatchGuard> where F: FnOnce(&str, Option<&str>) -> TestBinaryDispatchMode
Description: Reads argv0 and argv1, calls classify(exe_name, argv1) to determine the dispatch mode, and acts accordingly. Intended to be called from a #[ctor] static initializer in test binaries.

Type: Struct
Name: TestCodexHelperPaths
Location: codex-rs/exec-server/tests/common/exec_server.rs
Description: Holds paths to the helper binaries needed by exec-server tests.
Fields:
  pub codex_exe: PathBuf
  pub codex_linux_sandbox_exe: Option<PathBuf>

Type: Function
Name: test_codex_helper_paths
Location: codex-rs/exec-server/tests/common/exec_server.rs
Signature: pub(crate) fn test_codex_helper_paths() -> anyhow::Result<TestCodexHelperPaths>
Description: Returns the paths to the codex helper binary and, on Linux, the codex-linux-sandbox binary, obtained from the current test binary and the dispatch guard.

Type: Function
Name: probe_remote_linux_sandbox
Location: codex-rs/core/tests/common/test_codex.rs
Signature: fn probe_remote_linux_sandbox(container_name: &str, remote_linux_sandbox_path: &str) -> Result<()>
Description: Runs the remote linux sandbox binary inside the given Docker container using a read-only sandbox policy and /tmp as the working directory, targeting /bin/true. Returns an error that includes stdout and stderr if the command fails.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.