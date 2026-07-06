## Description

The fluent attribute container class in the support package is missing many useful data-access and manipulation methods. Currently, calling methods such as ones that retrieve a typed value (integer, float, boolean, string, array, collection, date, or enum) on the container do not work as expected — instead of executing the intended logic, these calls are silently interpreted as attribute assignments by the generic magic method handler. This makes the class far less useful as a structured data holder.

## Expected Behavior

- Setting an attribute using a dot-notation path (e.g., nested keys) should work correctly and affect the correct nested position in the attributes.
- A method to bulk-merge additional attributes into the container should exist and return the container instance for chaining.
- A method to retrieve a subset of attributes as a fresh container instance, using a dot-notation path, should be available and should wrap the resolved value appropriately.
- Type-safe retrieval methods should exist for strings, booleans, integers, floats, arrays, and collections, each returning the correct native type.
- A date-parsing method should return a date object or null for missing/null values, and should throw an error for genuinely invalid date values or invalid format strings.
- Methods for resolving backed enum instances (single and multiple) from stored values should be available, returning null or empty array for missing/invalid cases.
- The class should be extensible via a macro mechanism, allowing custom methods to be registered and invoked dynamically.

## Why This Matters

Without these methods, developers using the fluent container have to retrieve raw attributes and cast them manually, losing the expressiveness of the fluent API. The missing type-safe accessors, date parsing, enum resolution, and macro support limit the usefulness of the class significantly compared to similar containers in the PHP ecosystem.
