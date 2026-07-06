I'm using the JSON containment find operator in TypeORM to query JSONB columns.

*   The JsonContains function must accept arrays as its argument — both arrays of objects and arrays of primitive values (e.g., numbers) — without requiring any unsafe type cast at the call site.

*   When JsonContains is used in a where clause for a JSONB column that stores an array, it must perform partial array containment matching: only records whose JSONB column contains all elements from the provided array are returned.

*   When a JSONB array column contains all the elements in the JsonContains argument (partial containment), the corresponding entity must be included in the query result.

*   When a JSONB array column does not contain any of the elements in the JsonContains argument, no entities are returned.

*   Array containment must work correctly for both arrays of objects (e.g., [{id: 'x', value: 'y'}]) and arrays of primitive values (e.g., [5, 6]) stored in JSONB columns.

*   The JsonContains function must be usable with PostgreSQL and CockroachDB drivers for JSONB column queries.


*   Interface details: Type: Function
Name: JsonContains
Location: src/find-options/operator/JsonContains.ts
Signature: JsonContains<T extends Record<string | number | symbol, unknown> | readonly unknown[]>(value: T | FindOperator<T>): FindOperator<any>
Description: A find operator that generates a JSONB containment query. The generic type constraint must allow both plain objects (Record<...>) and readonly arrays (readonly unknown[]), so that callers can pass arrays of objects or arrays of primitive values without a type cast. Returns a FindOperator wrapping the provided value. The function must be exported from the module so it can be imported by consumers as a named export.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.