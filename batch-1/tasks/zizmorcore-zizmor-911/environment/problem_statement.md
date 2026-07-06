## Description

The GitHub Actions security scanner produces inaccurate results when analyzing workflow steps that reference environment variables through the expression context syntax. Specifically, when a workflow accesses an environment variable by name through the expression language (rather than through the standard shell variable syntax), the scanner fails to correctly determine whether that variable is "static" — i.e., whether its value is controlled by the platform rather than by user-supplied input.

This causes two related problems:

1. **False negatives**: References to unknown or user-defined environment variables through the expression context are incorrectly treated as static (safe), when they may be dynamically set by workflow logic and therefore potentially unsafe.

2. **False positives / missed suppressions**: References to well-known, platform-provided runner environment variables (the ones the runner always sets automatically) are sometimes not suppressed, even though those variables are always static and cannot be influenced by attackers.

## Expected Behavior

- When a workflow step reads a platform-provided runner environment variable through the expression context, the scanner should recognize it as static and suppress the finding (or produce only a low-severity pedantic note).
- When a workflow step reads an unknown or user-defined environment variable through the expression context, the scanner should treat it as potentially non-static and report it as a finding.
- The overall finding counts for affected workflows should reflect these corrected suppression and detection behaviors.

## Why This Matters

Incorrect suppression logic leads to noisy output (spurious warnings on safe patterns) and missed coverage (silent passes on potentially dangerous patterns), reducing the usefulness and trustworthiness of the scanner's reports.
