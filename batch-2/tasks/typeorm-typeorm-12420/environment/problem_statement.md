## Description

TypeORM's JSON containment find operator only supports querying JSONB columns that store plain objects. When a JSONB column stores an array (either an array of objects or an array of primitive values), using this operator requires an unsafe type cast to avoid TypeScript errors. Moreover, even with the cast, the query may not be generated correctly when the value is an array — resulting in incorrect or broken database queries.

## Expected Behavior

- The JSON containment find operator should accept arrays as its argument without requiring an unsafe type cast
- When querying a JSONB column that stores an array of objects, partial containment filtering should work correctly — only records where the column's array contains all the specified objects should be returned
- When querying a JSONB column that stores an array of primitive values (e.g., numbers), partial containment filtering should also work correctly
- Records that do not contain the specified elements should not be returned

## Why This Matters

Many real-world database schemas use JSONB columns to store arrays of structured data (e.g., tags, metadata, connection parameters). Without array support in the containment operator, developers cannot use the standard find API for these columns and must resort to raw queries or type workarounds. Proper array support enables clean, type-safe querying of these common data patterns.
