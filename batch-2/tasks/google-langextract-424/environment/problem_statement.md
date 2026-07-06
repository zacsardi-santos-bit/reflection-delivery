## Description

The OpenAI provider currently only supports real-time completions, which are relatively expensive for high-volume workloads. OpenAI offers an asynchronous batch processing endpoint that is significantly cheaper for large sets of requests, but the library has no way to use it. Users working with many documents at once must pay full real-time prices and deal with rate limits that wouldn't apply to batch jobs.

## Expected Behavior

- A new batch processing module should handle the full lifecycle of submitting requests to the asynchronous batch endpoint: uploading input files, creating jobs, polling for completion, and returning responses in the original input order.
- The language model should accept a batch configuration option with at least an enabled flag and a minimum-prompt threshold. When the number of prompts meets the threshold, requests are routed to the batch endpoint automatically; when below it, real-time calls are used with an informational log message.
- Batch configuration should support a polling interval, a timeout (defaulting to more than 24 hours to cover the full completion window), a per-job request limit for splitting large workloads, optional metadata to attach to batch jobs, and an optional callback invoked after each job is created.
- Error handling should cover all notable failure modes: invalid configuration, job creation failures (with uploaded file cleanup), terminal job statuses (failed, expired, cancelled), per-item errors in the output, non-successful HTTP responses, model refusals, missing results, unknown result identifiers, file download permission errors, and timeouts (with automatic job cancellation).
- Batch configuration should be provider-local and must not be forwarded to the underlying real-time completions API.

## Why This Matters

High-volume document processing is one of the primary use cases for this library, and the lack of batch support forces users to pay significantly higher costs for workloads that could otherwise be processed asynchronously. Adding first-class batch support makes the library practical for large-scale production workflows.
