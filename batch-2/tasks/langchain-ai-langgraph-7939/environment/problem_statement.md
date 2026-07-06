## Description

When deploying a LangGraph application through the CLI, there is currently no way to record which specific third-party packages are being used, or at which versions they are installed. This makes it impossible to know — at the deployment revision level — what dependencies were bundled at the time of a given deployment.

We should add the ability to scan a project's dependency files and detect the presence and version of a set of known packages of interest. The scanner should handle all common dependency specification formats — lock files with resolved versions, project manifest files with version constraints, and plain requirements files — and extract the most accurate version information available. Exact resolved versions (from lock files) should take priority over declared constraints (from manifests or requirements files). When a package is referenced without a version, it should still be recorded but marked as having an unknown version.

The detected package information should then be forwarded to the backend service as part of deployment update calls, so it is available for tracking and auditing.

## Expected Behavior

- Scanning a project with a lock file returns the exact resolved version for any tracked package found there.
- Scanning a project with only a manifest file returns the declared version constraint.
- Scanning a project with only a requirements file returns the pinned version; bare references return an unknown marker.
- A tracked package referenced only as an extras dependency on another package returns an unknown marker.
- Packages that do not appear in any dependency file are not included in the result.
- Dependency paths that escape outside the project directory are silently skipped.
- Non-path dependency entries are silently ignored.
- Very large dependency files are read up to a size cap without raising errors.
- Multiple dependency directories are scanned in order; the first match wins.
- Deployment update operations accept an optional list of tracked package strings, forwarding them at the top level of the request — not nested inside other configuration fields.

## Why This Matters

Without this feature, there is no automated way to know whether a specific deployment revision includes a given third-party package or which version it uses. Adding dependency tracking enables better auditing, version visibility, and downstream tooling for LangGraph deployments.
