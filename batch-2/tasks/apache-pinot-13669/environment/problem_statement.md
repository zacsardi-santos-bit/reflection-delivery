## Description

When reading Avro records that contain logical type fields (timestamps, decimals, UUIDs, etc.) nested inside complex structures like arrays, maps, or nested records, the current logical type conversion logic does not recursively descend into these nested structures. As a result, values that should be converted to their properly typed logical equivalents (such as time-based values, decimal numbers, or unique identifiers) remain as their raw Avro representations (binary data, integers, or text) after conversion.

## Expected Behavior

- Logical types nested inside arrays should be converted element by element
- Logical types nested inside maps should be converted value by value
- Nested records should be recursively converted so that their own logical type fields are also processed
- All of this should work together in a complex, multi-level schema where arrays contain records with map fields that themselves use logical types

## Current Behavior

Currently, only top-level logical type fields on a record are converted. Any logical type fields that appear inside an array, map, or sub-record are left as their raw Avro types, leading to incorrect downstream behavior.

## Why This Matters

Real-world Avro schemas frequently use complex nested structures with logical types embedded at various levels. Without recursive conversion, data pipelines that depend on these logical type conversions receive incorrect types and cannot process the data correctly.
