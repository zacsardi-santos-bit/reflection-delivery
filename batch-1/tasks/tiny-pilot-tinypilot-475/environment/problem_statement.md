## Description

TinyPilot needs a way to persist and retrieve update job results. Currently, there's no structured way to store the outcome of update operations, including whether the update succeeded or failed, any error messages, and when the update occurred.

The system needs an `update_result` module that can:
1. Store update results in a structured JSON format
2. Read update result files back into Python objects
3. Handle ISO-8601 formatted timestamps for tracking when updates occurred

## Expected Behavior

- A `Result` dataclass should exist with fields for `success` (boolean), `error` (string), and `timestamp` (datetime)
- The `read()` function should parse JSON files with update results and return a `Result` object
- The `write()` function should serialize a `Result` object to JSON format
- Timestamps should be formatted as ISO-8601 strings like "2021-02-10T085735Z"
- When reading an empty JSON object `{}`, sensible defaults should be used: `success=False`, `error=''`, and `timestamp` set to Unix epoch

## Current Behavior

The `update_result` module does not exist. There is no way to read or write update result files, and no `Result` dataclass to represent update outcomes.
