## Description

The variable interpolation system in Bruno doesn't handle complex value types well. When users store objects, arrays, or date/time values in their environment variables or collection variables and reference them inside request templates, those values are either rendered incorrectly or produce unexpected output instead of a proper string representation.

Additionally, when an object key contains a dot character as a literal part of the key name rather than a path separator, there is currently no way to reference it unambiguously in a template — the dot is always interpreted as a path separator, making it impossible to distinguish a literal dotted key from a traversal path.

## Expected Behavior

- When a template variable holds an object or array value, it should be serialized to its JSON string representation and substituted into the template.
- Date and time library values assigned to variables should also be serialized to their standard quoted ISO string format when interpolated.
- A bracket-notation syntax should be supported within template references so that users can explicitly target properties with dot-containing or otherwise special key names, independently of the existing dot-path traversal syntax.
- Both bracket-notation and dot-notation references should coexist in the same template and resolve to their respective values correctly.
- Objects containing nested template placeholders should have those inner placeholders resolved before the object itself is serialized.

## Why This Matters

Users frequently work with structured data (objects, arrays) and time values in their Bruno collections. Being able to interpolate these values naturally into request bodies, headers, or scripts — and being able to unambiguously reference keys with special characters — removes a significant source of confusion and manual workarounds.
