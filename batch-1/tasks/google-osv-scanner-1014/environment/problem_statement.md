## Description

The osv-scanner tool does not currently support outputting vulnerability results in CycloneDX SBOM format. Many security and compliance workflows rely on CycloneDX as a standard interchange format, and users need to be able to produce CycloneDX-formatted bills of materials from their scan results, at both version 1.4 and version 1.5 of the specification.

To generate CycloneDX output correctly, the tool also needs utility functions that can parse ecosystem-specific package identifiers into their component parts. For example, packages from the Java ecosystem use a colon-separated "group:artifact" convention, Go modules use a slash-separated path where the last segment is the package name and the rest is the namespace, and PHP Composer packages use a slash-separated "vendor/package" convention. These parsing utilities should return an error for malformed or empty package names.

Additionally, a grouping function is needed to consolidate packages that appear in multiple lock files into a single entry per package, identified by its standardized package URL. When the same package appears more than once, its dependency group annotations should be combined into the merged entry.

## Expected Behavior

- A new output format flag value for CycloneDX 1.4 and another for CycloneDX 1.5 should be accepted by the scanner CLI
- When no vulnerabilities are found the exit code should be 0; when vulnerabilities are found the exit code should be 1
- Ecosystem-specific package name parsers should correctly split names into namespace and name components, and return errors for invalid or empty names
- The package grouper should deduplicate packages across sources, merging their dependency group annotations

## Why This Matters

Developers integrating osv-scanner into CI pipelines and software composition analysis workflows need CycloneDX output to feed results into downstream tooling. Without this format, they must either convert the output manually or use a different scanner, which is unnecessary friction.
