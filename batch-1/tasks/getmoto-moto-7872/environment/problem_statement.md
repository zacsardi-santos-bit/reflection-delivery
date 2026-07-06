## Description

The AWS mock library for Python does not currently support the next-generation SageMaker AutoML v2 job APIs. Developers who write code that uses these newer AutoML endpoints cannot test it with the mock library because the mock simply doesn't recognize these API calls. The mock should be extended to support the full AutoML v2 workflow.

## Expected Behavior

The SageMaker mock should support:

- **Creating** an AutoML v2 job with all relevant configuration including data input config, output config, problem type config (image classification, text classification, tabular, time series forecasting, text generation), role, security config, objectives, deploy config, and tags. Creation should return a properly formatted ARN.
- **Describing** a created job, returning all stored parameters alongside mock-filled response fields (job status, timestamps, best candidate details, artifacts, resolved attributes, etc.)
- **Listing** AutoML jobs with support for filtering by name substring, status, creation time, and last modified time, with configurable sort order and sort field.
- **Stopping** an AutoML job, which transitions the job from a running state to a stopped state.
- **Tagging**: Tags specified at creation should be retrievable; tags should also be addable and removable after creation via the standard SageMaker tagging APIs.
- **Resource discovery**: AutoML jobs with tags should appear when querying the Resource Groups Tagging API with a SageMaker resource type filter.

## Why This Matters

Without this support, developers are unable to write offline unit tests for application code that uses SageMaker AutoML v2. Adding this mock support enables fully offline testing without AWS credentials or network access.
