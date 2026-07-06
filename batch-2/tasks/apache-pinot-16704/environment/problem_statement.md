## Description

Pinot's existing sum aggregation function handles integer columns by converting values to floating-point numbers internally before accumulating the result. For workloads where the column is known to contain only integer data, this type promotion is wasteful — it adds conversion overhead and foregoes the efficiency of native integer arithmetic. A specialized sum aggregation optimized for integer columns would eliminate this overhead and return results as 64-bit integers directly.

## Expected Behavior

- A new integer-specific sum aggregation function is available in SQL queries by a distinct name, resolved case-insensitively.
- The function produces results as a 64-bit integer (LONG) for both intermediate and final output.
- When null handling is disabled, null values in the input are treated as the column's stored default null value and contribute to the sum as normal entries. A result of zero is returned when all inputs are null and no prior partial sum exists.
- When null handling is enabled, null values are excluded from the sum entirely. If all values in a group are null, the group returns null rather than zero.
- The function supports plain aggregation queries as well as group-by queries over both single-value and multi-value dimensions, with consistent null handling behavior in all cases.

## Why This Matters

Users aggregating large integer datasets benefit from eliminating the overhead of converting integers to floating-point numbers during aggregation. This is particularly valuable in performance-sensitive pipelines where all column values are known to be integral and the cost of type promotion is avoidable. Returning long integer results also avoids the precision loss that can occur when large integers are represented as doubles.
