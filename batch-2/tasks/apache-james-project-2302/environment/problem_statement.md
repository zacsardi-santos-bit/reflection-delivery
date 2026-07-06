## Description

Several of our test dependencies are out of date and the tests need to be updated to use the newer APIs they expose. The most visible change is that the JSON comparison library we use has a new major version that simplifies how comparison options are passed: rather than wrapping them in a special container object, options can now be supplied as individual arguments directly to the comparison method.

A related change affects how the system reports errors when message metadata contains a malformed date: the older library version surfaces this failure as a generic "no such element" error, while the updated version correctly raises a date-and-time-specific error. The tests have already been updated to expect the more precise error type, but the underlying library versions have not been updated yet.

## Expected Behavior

- JSON comparison calls should compile and run using the simplified varargs-style option arguments rather than the legacy container-object style.
- When deserializing message metadata with an invalid date value, the system should raise a date-and-time-specific error rather than a generic missing-element error.

## Why This Matters

Keeping library dependencies current avoids security vulnerabilities and compatibility problems with the Java runtime. It also improves the clarity of error messages — knowing that a date field is malformed (rather than merely absent) helps diagnose serialization bugs faster.
