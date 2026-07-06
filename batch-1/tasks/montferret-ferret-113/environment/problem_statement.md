## Description

The ferret query language is missing a built-in function to construct an object from two parallel arrays: one containing field names and one containing corresponding values. ArangoDB's query language supports this operation natively, and since ferret aims to be compatible with ArangoDB query semantics, ferret should support it too.

## Expected Behavior

- Users should be able to pass a same-length array of string keys and an array of any values and receive back a fully-formed object where each key is paired with its corresponding value.
- The function should require exactly two arguments, both of which must be arrays.
- All elements in the keys array must be strings; any non-string key should be rejected with an error.
- If the two arrays have different lengths, the function should return an error.
- When the keys array contains duplicate entries, only the first mapping for each key should be preserved — later occurrences of the same key are ignored along with their values.

## Why This Matters

Without this function, users who have data split across two parallel arrays cannot easily construct an object in a single query expression. This addition brings ferret's object-manipulation library in line with ArangoDB behavior and makes it straightforward to assemble objects dynamically from separate key and value collections.
