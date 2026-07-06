## Description

The "no restricted imports" lint rule supports two ways to configure restricted modules: a simple array of module name strings, or an array of configuration objects where each object includes the module name plus a custom error message. However, when using the object configuration style directly in the top-level array (rather than nesting entries under a special sub-key), the restriction is silently ignored — imports from the restricted module are not flagged at all.

## Expected Behavior

- When the rule is configured with an array entry that is an object containing a module name (and optionally a custom message), any import from that module should be reported as a violation.
- The object-style configuration format should work the same way as the string-style format, just with an optional custom message attached.
- It should be possible to mix plain string entries and object entries in the same configuration array.

## Why This Matters

Developers who rely on the object format to attach descriptive messages to restricted imports are currently getting no enforcement at all — a very confusing silent failure. The fix ensures that both configuration styles are treated consistently and restrictions are always applied regardless of which format the developer uses.
