## Description

The Beam SQL extension does not currently support population variance or sample variance as SQL aggregate functions. This means users who want to compute statistical variance over a dataset using Beam SQL must implement variance calculations themselves outside of SQL, which is cumbersome and limits the usefulness of the SQL interface for analytical workloads.

## Expected Behavior

- Users should be able to write SQL queries using population variance and sample variance aggregate functions against a PCollection and get correct variance results.
- For numeric columns, the population variance should divide by the number of elements (n), while the sample variance should divide by (n - 1).
- Both integer and floating-point column types should be supported. For integer columns, the result should be an integer (truncated). For double columns, the result should be a precise floating-point number.
- The aggregation should support parallel/distributed computation by enabling partial accumulators to be combined correctly using the parallel variance formula.

## Why This Matters

Statistical aggregation functions like variance are fundamental to data analysis. Without support for these functions in Beam SQL, users cannot perform common analytical tasks declaratively and must resort to lower-level pipeline code. Adding population variance and sample variance makes Beam SQL more complete and on par with standard SQL dialects used in data warehouses and analytical platforms.
