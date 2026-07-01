## Description

Custom validation rules in this enforcement library have no way to know where they are being executed within a larger validation schema. When a rule runs on a nested field inside an object schema or on an element inside an array, it only sees the current value — it has no access to the surrounding context, such as which key is being validated, what index the array element is at, or what the parent object's values look like.

This makes it impossible to write cross-field validation rules that depend on context. For example, there is currently no way to check whether each item in a friends list matches a specific field value on the parent object, because the custom rule can't reach up to the parent scope.

## Expected Behavior

- Custom rules should be able to retrieve a "context" object that describes the current validation scope.
- The context should expose the current value being validated, any relevant positional metadata (such as the field key name when inside an object schema, or the index when inside an array), and a way to traverse to the parent scope.
- Traversal should be chainable: if you are three levels deep, you should be able to reach the top-level context by walking up the parent chain.
- At the outermost level, the parent traversal should return a sentinel indicating there is no further parent.

## Why This Matters

Without this capability, custom rules are stateless with respect to the validation hierarchy. Adding context access allows developers to write sophisticated cross-field rules — for instance, validating that no entry in a list duplicates a value stored elsewhere in the same data structure.
