## Description

When users interact with forms containing optional nested sections and submit the form without filling in any of those optional fields, the resulting form data includes empty object structures (e.g., objects where all fields are blank or have no meaningful value). These hollow objects carry no meaningful information, yet they appear in the submitted data and can cause problems for consuming systems or APIs that don't expect empty containers.

There is currently no utility in the library that strips these empty optional sub-objects and empty optional scalar fields from form data before it is consumed. A new utility function is needed that can clean up form data by removing optional properties whose values are entirely empty — recursively, through deeply nested structures — while leaving required properties intact.

## Expected Behavior

- Optional object properties that contain only empty values (empty strings, absent or unset values, empty arrays, or nested objects that are themselves empty) should be removed from the result.
- Required properties should not be removed, even if their values are empty.
- The cleanup should work recursively through nested object structures.
- Arrays should be processed element-by-element, applying the appropriate schema to each item.
- Scalar properties that are optional and have empty string values should also be removed.
- Non-empty values, including boolean false and the number zero, should always be preserved.
- Properties that appear in the form data but have no corresponding schema entry should be left as-is.
- When all properties of an object are pruned away, the result should be absent rather than an empty object.

## Why This Matters

Submitting forms with empty nested objects can confuse downstream consumers that expect either meaningful data or nothing at all. This utility gives developers a way to ensure only data that actually has content is passed along, making form submission cleaner and safer.
