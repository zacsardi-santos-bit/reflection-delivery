## Description

When pull requests bump or add Python package dependencies in this project, there is currently no automated way to verify the supply-chain security of those packages. Specifically, we lack tooling to check whether a newly-added or updated package was built and published through a trusted, verifiable automated pipeline, and to flag packages that require further human or agent review.

We need a new script that can be invoked as part of CI to analyze PR diffs, identify which packages changed in tracked requirement files, look up each package on the public package index, and verify whether a provenance attestation exists for the published release. The script should produce a structured report and a formatted comment summarizing the security status of each package change.

## Expected Behavior

- The tool parses a unified diff and detects package version changes only in files that match the tracked requirement file pattern; changes in other file types (documentation, project configuration, etc.) are ignored.
- Package names are normalized before lookup so that naming variations do not cause missed matches.
- For each changed package, the tool checks whether the published release has a trusted provenance attestation from a recognized automated publisher.
- Results are categorized as passed, warning, failed, or needing further review depending on what was found.
- A formatted summary is generated that can be posted as a pull request comment; it collapses details when everything passes, and expands with placeholders for an AI agent when human-level review is needed.
- The tool can be invoked from the command line, reads a diff file and a PR number, and writes a JSON artifact with the full results.
- If the diff file path provided does not exist, the tool exits with an error.

## Why This Matters

Automated dependency updates are a common attack vector for supply-chain compromises. By automatically checking whether package releases are backed by verifiable, trusted publishing pipelines, we can catch suspicious or unverified dependency bumps before they are merged — without requiring a human to manually inspect every update.
