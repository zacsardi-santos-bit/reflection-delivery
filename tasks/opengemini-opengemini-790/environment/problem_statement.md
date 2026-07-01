## Description

OpenGemini is missing a dedicated high-performance binary write service for ingesting time-series records. Currently, there is no way for clients to push batches of records using a binary protocol with support for partial success handling and authentication.

## Expected Behavior

A new gRPC-based write service should be added to OpenGemini that:

- Accepts batch write requests containing multiple time-series records in binary-encoded format
- Validates each record in the batch individually
- Returns a success response when all records in the batch are valid and written
- Returns a failure response when all records in the batch are invalid
- Returns a partial success response when some records are valid and some are invalid (valid records should still be written)
- Responds to health-check ping requests by reporting that the server is up
- Supports optional authentication and authorization, so write requests can be validated against user credentials and write privileges when authentication is enabled

## Why This Matters

Without this service, binary-protocol clients have no way to write record batches to OpenGemini efficiently. The partial success response is particularly important because it allows clients to know that some of their data was accepted even if part of the batch was malformed, rather than receiving an opaque all-or-nothing failure.
