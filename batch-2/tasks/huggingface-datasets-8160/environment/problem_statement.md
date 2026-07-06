## Description

The datasets library should support loading time-series files used in IoT and sensor-data workflows. Currently there is no built-in way to load this format through the standard dataset-loading infrastructure, forcing users to write custom parsing code.

## Expected Behavior

- Users should be able to point the standard data loader at one or more of these time-series files and receive a structured dataset back.
- The resulting dataset should be in "wide" format: one row per unique device or sensor identity (identified by its categorical/tag columns), with the measurements over time stored as aligned lists.
- When the same device appears across multiple files, its records should be automatically merged into a single row with all time points sorted chronologically.
- Fields that appear in some files but not others should be filled in with null values for the time points where they are absent.
- When the same field has different numeric precision across files, the wider type should be used for the merged result.
- Users should be able to filter which fields to include, apply time-range constraints, select a specific table from multi-table files, and attach a timezone to timestamps.
- Corrupted files should be handleable with configurable behavior: raise an error, skip silently, or skip with a warning.
- Streaming mode should be supported.
- All common data types (boolean, integer, floating-point, text, timestamp, date, binary) should load correctly.

## Why This Matters

Many real-world sensor and IoT datasets are stored in this file format, and having a native loader removes the friction of writing bespoke parsing pipelines. It also makes the data accessible through the same uniform API that users already rely on for other formats.
