## Description

The OpenAI platform offers a Batch API that lets developers submit large numbers of requests in a single asynchronous job, with results delivered within 24 hours at a reduced cost. The Kotlin client library currently has no support for this feature — there are no data types to represent batch jobs, batch requests, or batch outputs, and the client has no methods for creating, listing, retrieving, or cancelling batches.

## Expected Behavior

- The library should provide typed data structures for representing a batch job, including its unique identifier, the API endpoint being targeted, and the completion time window.
- The library should provide typed structures for batch input lines (per-request entries with a custom ID, HTTP method, URL, and request body) and batch output lines (responses with a status code and response body).
- A completion window type should exist with at least a 24-hour option.
- Batch input and output data structures must be serializable/deserializable to and from JSON (and JSONL format for files).
- The client should expose methods to create a batch from an uploaded file, retrieve a batch by ID, list all batches, and cancel an in-progress batch.
- The response body within a batch output entry should be usable as a chat completion result.

## Why This Matters

Without Batch API support, Kotlin developers using this library cannot take advantage of the asynchronous bulk processing feature. This forces them to either send individual real-time requests (at higher cost) or build their own serialization/deserialization layer outside the library.
