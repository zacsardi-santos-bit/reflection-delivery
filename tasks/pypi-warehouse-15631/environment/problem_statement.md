## Description

The package upload system currently mixes HTTP form handling and Python package metadata validation in one large module. This makes the validation logic hard to reuse, difficult to test in isolation, and produces inconsistent or unclear error messages when package authors submit invalid metadata.

We need to extract the metadata parsing and validation logic into its own dedicated module. This new module should be able to parse metadata from either an uploaded package archive (raw bytes) or an HTTP form submission, validate all standard metadata fields, and report validation failures in a structured way that identifies exactly which field is invalid.

Additionally, the upload form handling logic (validators and form class) should be moved to its own module to keep responsibilities cleanly separated from the core upload handler.

## Expected Behavior

- A dedicated metadata module can parse package metadata from either file bytes or form post data
- When metadata is invalid, structured exceptions are raised that identify the specific field that failed validation (not just a generic error)
- Metadata validation includes: version format, unsupported metadata versions, field length limits, email address format, classifier validity (including deprecation), URL scheme restrictions, and prohibition on direct URL dependencies
- Deprecated classifiers are rejected by default but can be allowed via an opt-in flag (for backfill scenarios)
- Form data handling correctly converts comma-separated keywords into lists, project URL entries into dictionaries, and treats empty string values as absent
- Duplicate values for single-value fields or duplicate project URL labels are detected and reported
- The upload form validators and form class live in a dedicated forms module
- The form validates that at least one file digest is provided, and that wheel uploads include a Python version

## Why This Matters

Separating metadata validation from form handling makes each piece easier to test independently, enables the validation logic to be reused in other contexts (e.g., validating metadata from a file without going through a form), and produces better error messages that help package authors understand exactly what is wrong with their submission.
