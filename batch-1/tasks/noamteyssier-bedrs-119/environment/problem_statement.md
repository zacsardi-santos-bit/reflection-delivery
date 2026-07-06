## Description

The library provides powerful tools for working with genomic intervals, but it currently lacks out-of-the-box struct types that correspond to the standard BED format columns. Users who want to work with BED3, BED4, BED6, or BED12 formatted genomic data must roll their own types or adapt lower-level primitives, which creates unnecessary friction.

## Expected Behavior

The library should provide named, ready-to-use types for each of the common BED format variants:

- A 3-field type representing a genomic interval with chromosome, start, and end
- A 4-field type that extends the 3-field type with a name
- A 6-field type that further adds score and strand information
- A 12-field type covering the full BED specification

Each type should be constructible directly (passing the relevant fields), should expose its fields through accessor methods, and should implement the existing coordinate traits so they work seamlessly with the rest of the library.

These types should be importable directly from the crate root, making them immediately accessible to users without needing to navigate module paths.

## Why This Matters

Providing these concrete, named types aligned with the BED specification greatly reduces boilerplate for the most common use cases and makes the library immediately usable for standard bioinformatics workflows. Users can create and manipulate BED-format intervals in a natural, readable way without needing to understand the library's internal generics system upfront.
