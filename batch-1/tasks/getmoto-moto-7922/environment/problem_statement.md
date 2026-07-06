## Description

The moto library currently has no support for SageMaker Model Cards. Developers who build machine learning workflows that use Model Cards cannot write offline unit tests for this functionality — they must either hit the real AWS service (incurring cost and requiring credentials) or skip testing entirely.

Model Cards are an AWS SageMaker feature for documenting machine learning models, capturing details like model descriptions, intended use, and performance. They are versioned, taggable resources. Without mock support for these APIs, it is impossible to test code that creates, updates, inspects, or removes model cards in a CI/CD pipeline or local development environment.

## Expected Behavior

- Creating a model card should return an ARN and raise a conflict error if the card already exists
- Updating a model card should create a new version, and raise a not-found error for unknown cards
- Listing model cards should support filtering by name, status, and creation time range, and support sort order and sort field options
- Listing model card versions should support filtering by status and sort order, and expose version numbers and timestamps
- Describing a model card should return full details including the content, status, security config, and version; it should support fetching a specific version or defaulting to the latest
- Deleting a model card should remove it from listings and raise a not-found error if it doesn't exist
- Tags should be associatable with model cards at creation, and be manageable (add, list, delete) using the standard tagging operations

## Why This Matters

This unblocks teams that use SageMaker Model Cards from writing proper unit and integration tests for their ML workflows using the moto mock library.
