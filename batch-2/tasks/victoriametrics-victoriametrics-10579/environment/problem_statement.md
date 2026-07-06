## Description

The Graphite tag series registration endpoints currently accept requests and process them by creating new time series in the database. This behavior was not intentional — calling these endpoints creates side effects (new time series) even when callers may simply be trying to "tag" an existing series. These endpoints should not be silently creating data.

## Expected Behavior

- When a client sends a request to register a single Graphite tag series, the server should respond with "not implemented" rather than accepting the request and creating new time series.
- When a client sends a request to register multiple Graphite tag series, the server should likewise respond with "not implemented" and should not create any new time series.
- The response must clearly communicate that these operations are not supported.

## Why This Matters

Users and operators may unknowingly cause unintended data ingestion when interacting with these endpoints. By explicitly returning a "not implemented" status, the server makes it clear that these Graphite tagging operations are not supported, prevents accidental time series creation, and avoids silent data accumulation in the database.
