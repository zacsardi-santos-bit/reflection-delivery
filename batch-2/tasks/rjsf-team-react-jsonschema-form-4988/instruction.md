I'm working on a form library that uses JSON Schema to define form structure.

*   The removeOptionalEmptyObjects function must return undefined when formData is undefined.

*   The removeOptionalEmptyObjects function must return formData unchanged when it is not an object or array (e.g., a string value).

*   The removeOptionalEmptyObjects function must return formData unchanged when the schema argument is null or undefined.

*   The removeOptionalEmptyObjects function must return formData unchanged when the schema has no 'properties' key (including when the schema type is not 'object' or the schema is an object type with no properties defined).

*   For each property in schema.properties that also exists in formData: the function must recursively process its value. A value is considered 'empty' if it is undefined, null, an empty string (''), an empty array ([]), or an object that after recursive processing yields no non-undefined properties (i.e., results in undefined). Boolean false and number 0 must NOT be considered empty.

*   If a property's processed value is empty AND the property key is NOT listed in the parent schema's 'required' array, the key must be removed from the result object entirely.

*   If a property's processed value is empty AND the property key IS listed in the parent schema's 'required' array, the key must remain in the result object with its value set to undefined.

*   Properties present in formData but not defined in schema.properties must be preserved in the result as-is, without modification.

*   When all properties of a result object are pruned (deleted or set to undefined) and the result would be an empty object with no meaningful data, the function must return undefined rather than an empty object.

*   When formData is an array and schema.items is a single schema object, each array element must be processed using that schema. If no changes occur, the original array is returned as-is. If schema has no items definition, the array is returned as-is.

*   When formData is an array and schema.items is an array (tuple schema): each element at index i uses schema.items[i] as its schema; elements at indices beyond the length of schema.items use schema.additionalItems if defined; elements with no applicable schema (out-of-bounds with no additionalItems) are left as-is unchanged.

*   Optional scalar properties (non-object, non-array) with empty string values must also be removed when they are not listed in the parent schema's required array.

*   Nested optional object structures must be processed recursively: if an inner optional object becomes empty after processing, it is treated as empty at the parent level, potentially causing the parent optional object to also be removed.


*   Interface details: Type: Function
Name: removeOptionalEmptyObjects
Location: packages/utils/src/removeOptionalEmptyObjects.ts
Signature: removeOptionalEmptyObjects(validator: ValidatorType, schema: RJSFSchema, rootSchema: RJSFSchema, formData: any) -> any | undefined
Description: Default export. Recursively traverses formData against the given schema and removes any optional properties (object, scalar, or array) whose values are considered "empty" (undefined, null, empty string, empty array, or a nested object that is itself fully empty after processing). Required properties (listed in the schema's required array) are NOT removed — if a required property's value is processed to empty, its key is retained but set to undefined. Properties present in formData but not defined in the schema are preserved as-is. When all properties of an object are pruned, the function returns undefined instead of an empty object. Arrays are processed item-by-item using the appropriate per-item schema (tuple items by index, additionalItems for out-of-bounds indices, or as-is when no schema applies). Boolean false and number 0 are NOT considered empty.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.