## Description

Trivy currently supports several Python package managers (pip, Pipenv, Poetry, uv), but it lacks support for the new standardized Python lock file format introduced by PEP 751. Users working with Python projects that use this modern lock file format have no way to scan their dependencies for vulnerabilities with Trivy.

Additionally, the existing pyproject.toml parsing does not capture optional dependency groups (extras). This means that development tools, testing frameworks, and other optional dependencies defined in optional dependency sections are not recognized when determining which packages are direct dependencies.

## Expected Behavior

- Trivy should detect and parse the new standardized Python lock file format (both the default filename and the named variant with a single-segment identifier).
- Trivy should also recognize pyproject.toml as a companion file to determine which packages are direct vs. indirect (transitive) dependencies.
- Packages listed as direct dependencies in pyproject.toml — including those in optional dependency groups — should be classified as direct. All other packages should be classified as indirect. If the project itself is present in the lock file, it should be treated as the root package.
- Scanning a repository containing such a lock file should report vulnerabilities found in its packages.
- Files that look like the lock file format but don't comply with the naming rules (e.g. multiple segments in the identifier, or an empty identifier) must not be mistakenly detected.

## Why This Matters

Python projects are increasingly adopting the PEP 751 lock file format as a cross-tool standard. Without this support, Trivy users working with these projects cannot get vulnerability scanning coverage for their dependencies. Supporting this format — including correct direct/indirect dependency classification — gives users the same level of insight they already have for other Python package managers.
