## Description

The scripting engine is missing a built-in object-merging capability. Currently, when writing queries that need to combine several data objects into a single unified result, there is no standard function to do this. Developers have to work around this limitation manually.

## Expected Behavior

A new built-in function should be added to the objects standard library that:

- Accepts either multiple individual objects as separate arguments, or a single list containing objects
- Combines all properties from the provided objects into one new object
- When the same key exists in multiple source objects, the value from the last source wins
- Returns a completely independent copy — later changes to the original objects must not affect the merged result
- Returns an empty object when given an empty list
- Returns an error when given no arguments, non-object arguments, or a list containing non-object elements
- Returns an error when given more than one list as arguments (only a single list is supported)

## Why This Matters

This is a common data transformation operation needed when aggregating or combining structured data in queries. Without a built-in merge function, users must implement this manually or restructure their queries, which is error-prone and verbose.
