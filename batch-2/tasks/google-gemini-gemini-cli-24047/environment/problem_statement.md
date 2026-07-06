## Description

The sandboxed file I/O system has several correctness and security issues that need to be fixed.

**Security: Command injection on Windows**
When writing or reading files through the sandbox on Windows, file paths are being interpolated directly into shell command strings. A path containing special characters (like quotes or semicolons) can break out of the intended command and execute arbitrary shell code. File paths must be passed safely via environment variables, not interpolated into command arguments.

**Missing access policies on file operations**
When the sandboxed file service reads or writes a file, it currently does not communicate any access policy to the sandbox. This means the sandbox may deny the operation or grant incorrect permissions. The file service should include an explicit policy granting read access (for reads) and both read and write access (for writes) to the target file path.

**File-not-found errors not propagated correctly**
When a sandboxed read fails because the file does not exist, the thrown error currently has no error code attached to it. Callers need to distinguish "file not found" from other failures. The error should carry the standard not-found code so callers can handle it appropriately.

**Non-existent path handling on Linux**
When a sandbox policy allows a path that does not yet exist on the filesystem, the Linux sandbox skips it entirely. This prevents creating new files through the sandbox. Instead, the sandbox should grant access to the parent directory when the target path doesn't exist yet.

**macOS workspace write default too permissive**
The macOS sandbox is granting write access to the entire workspace by default, even when no write permissions are requested. The default should be read-only unless write permissions are explicitly included in the request.

**Plans directory not created on demand**
When entering plan mode, the tool assumes the plans directory already exists. In fresh workspaces or sandboxed environments, this directory may not have been created yet, causing subsequent plan file writes to fail. The tool should create the directory if it is missing.
