## Description

The Linux sandbox argument builder currently handles path resolution internally — it receives raw paths and resolves symlinks itself (for workspace, allowed paths, forbidden paths, and include directories). This responsibility should be moved upstream so that the builder receives pre-resolved paths instead. Additionally, extra tool directories configured for inclusion in the sandbox are not currently scanned for sensitive credential files, meaning those files could be inadvertently exposed inside the sandbox.

## Problems

- The sandbox argument builder conflates two concerns: resolving paths (following symlinks to their real locations) and using those paths to build sandbox arguments. This makes it harder to reason about and test each concern independently.
- When the workspace directory itself is a symlink, governance files (like version-control ignore files) may not be accessible from both the original workspace path and the real path, leading to incomplete sandbox configuration.
- When extra tool directories are included in the sandbox, any sensitive credential files inside them are not explicitly protected, potentially leaking them to sandboxed processes.
- Virtual read commands targeting files in those extra tool directories are not properly allowed, even though those directories are intentionally included in the sandbox.

## Expected Behavior

- Path resolution (symlink following) should happen once, before the sandbox argument builder runs, and the builder should receive a consolidated, pre-resolved path structure.
- The pre-resolved structure should carry: the workspace with both its original and real path, forbidden paths, globally-included directories, policy-allowed paths, policy-read paths, and policy-write paths.
- When the workspace lives at a symlinked location, governance files should be exposed from both the original workspace path and the resolved real path.
- Directories included globally in the sandbox should be scanned for secret/credential files, which should then be explicitly protected inside the sandbox.
- Virtual read commands that target files within globally-included directories should be permitted.

## Why This Matters

Separating path resolution from argument construction makes the sandbox easier to reason about, test, and maintain. It also closes a security gap where credential files in included tool directories could be exposed, and fixes a correctness issue where symlinked workspaces did not have all governance files properly accessible.
