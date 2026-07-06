## Description

The Gradle lockfile analyzer in Trivy does not currently mark discovered packages as indirect dependencies, even though all entries in a Gradle lockfile are resolved (and thus potentially transitive) dependencies. It also operates only on a single lockfile at a time, rather than scanning an entire project directory, and it makes no attempt to enrich packages with license information or inter-package dependency relationships — even when a local Gradle artifact cache is available.

## Expected Behavior

- All packages discovered via Gradle lockfiles should be marked as indirect dependencies.
- The analyzer should accept a project directory as input and locate lockfiles within it, rather than requiring a single file to be provided directly.
- When a local Gradle artifact cache is available (via the standard environment variable that points to the Gradle user home directory), the analyzer should read artifact metadata from the cache and enrich each discovered package with:
  - Its declared software licenses
  - Its direct dependency relationships
- When no cache is present, the analyzer should still succeed and return the packages without enrichment.
- Artifact metadata parsing should correctly fall back to the cache path for missing group or version information, and should resolve dependency versions that are specified as references to project properties.

## Why This Matters

Without this capability, Trivy's Gradle support provides only a flat list of packages with no license data and no dependency graph information. This makes it harder to assess the compliance posture and true risk of Gradle-based Java projects, since indirect dependencies and their licenses are not surfaced.
