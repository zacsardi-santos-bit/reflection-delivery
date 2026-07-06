## Description

The vector SQL functions in this database are missing several basic arithmetic operations that are commonly needed for machine learning and similarity-search workloads. Specifically:

- There is no way to subtract two vectors element-wise in a SQL query.
- There is no way to sum all the elements of a single vector into a scalar value using SQL.
- There is no aggregate function that sums a column of vectors row-by-row into a single output vector.

Additionally, the internal utilities for converting between vector literal formats and binary encoding are currently inaccessible outside the function module. This makes it impossible to write integration tests in other parts of the workspace that need to verify vector aggregate behavior.

## Expected Behavior

- A scalar SQL function should be available that takes two vector arguments and returns a new vector containing the element-wise difference.
- A scalar SQL function should be available that sums all elements within a single vector and returns a scalar float.
- An aggregate SQL function should be available that, when applied to a column of vectors, returns a single vector that is the element-wise sum across all rows. If any row in the group is null, the aggregate result should be null.
- The vector conversion utilities should be accessible from other crates in the workspace so that integration tests can construct binary-encoded vectors and verify aggregation results.

## Why This Matters

Without these functions, users who store high-dimensional vectors in the database cannot express basic vector arithmetic directly in SQL and are forced to retrieve raw data and post-process it outside the database. Adding these operations enables in-database vector computation, which is essential for nearest-neighbor and similarity workflows.
