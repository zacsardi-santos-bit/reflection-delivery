# Maven Version Finder Fails to Upgrade Jenkins-Style Versioned Dependencies

## Description

Dependabot is incorrectly skipping valid upgrade candidates for Maven dependencies that use Jenkins' continuous delivery release conventions. These conventions embed a git commit hash directly in the version string (e.g., a version number followed by a hex string separated by a dot or hyphen), which identifies the exact source revision used in the build. Since these embedded hashes differ between versions, the current version type comparison logic treats the two versions as having incompatible suffixes and filters the newer version out — even when both versions follow the same git-hash pattern.

## Expected Behavior

- When both the current version and a candidate version contain embedded git commit hashes (following Jenkins plugin release conventions), Dependabot should recognize them as the same version type and suggest the upgrade.
- When only one of the two versions contains an embedded git commit hash, it should still be treated as a compatible upgrade candidate.
- Valid git commit hashes in version strings are 7 to 40 hexadecimal characters (a-f, 0-9), may optionally start with 'v', may contain underscores (Jenkins uses underscores to encode certain uppercase hex letters), and must contain at least one non-numeric hex letter.
- Strings that look like git SHAs but are too short, too long, all-numeric, or contain non-hexadecimal characters must NOT be treated as git SHAs.
- A non-standard "RELEASE"-prefixed version string that ends with digits (used by some Maven artifacts) must be treated as a stable release — not as a pre-release — so that Dependabot does not incorrectly suggest pre-release upgrades for it.

## Why This Matters

Jenkins plugins and similar artifacts routinely use this commit-hash-in-version approach. Because Dependabot cannot recognize the pattern, it refuses to suggest upgrades for these dependencies even when newer versions are available, leaving projects stuck on outdated plugin versions.
