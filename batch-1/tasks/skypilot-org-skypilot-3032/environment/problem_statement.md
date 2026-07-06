## Description

The storage system already enforces naming rules for AWS S3 and Google Cloud Storage, rejecting invalid bucket/container names early with a clear error message before any cloud API calls are made. However, Azure Blob Storage containers have no equivalent validation — users can attempt to create a container with a name that violates Azure's naming requirements and only receive a confusing error from the cloud rather than a helpful, upfront message.

## Expected Behavior

Azure container name validation should be added to match the behavior already in place for S3 and GCS. Specifically, the system should reject:

- Names shorter than 3 characters or longer than 63 characters
- Names containing uppercase letters
- Names that do not start with a lowercase letter or digit
- Names containing two consecutive hyphens

Attempting to use any such name should raise a storage name error with a descriptive message, before any network calls are attempted.

## Why This Matters

Without early validation, users receive cryptic errors from Azure instead of actionable feedback. This is inconsistent with the experience for other supported cloud providers. Adding Azure container name validation improves usability and makes the system behave uniformly across all supported cloud storage backends.
