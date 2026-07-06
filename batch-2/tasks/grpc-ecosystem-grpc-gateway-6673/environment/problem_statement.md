## Description

The OpenAPI v3 generator currently emits every service, method, field, and enum value defined in a proto file, regardless of any visibility annotations attached to those elements. Many projects use visibility annotations to distinguish between internal, preview, and publicly released API surface — but since the generator ignores those annotations, it's impossible to produce an audience-specific specification. Internal endpoints, unreleased fields, and restricted enum values all end up in every generated document.

## Expected Behavior

- Elements annotated with a visibility restriction should be excluded from the generated output unless their restriction label matches one of the configured visibility selectors.
- Elements without any visibility annotation should always appear in the output.
- The feature should apply consistently across all element types: services, methods, message fields (in request bodies, query parameters, and component schemas), and enum values.
- When a service is hidden, its tag must not appear in the document's tag list.
- When filtering reduces a mutually-exclusive field group to a single remaining member, the mutual-exclusion constraint should be dropped entirely rather than left in an inconsistent state.
- When all values of an enumeration are hidden, the enum's component schema should still be emitted (since visible fields may reference it), but as an unconstrained type rather than an empty or invalid value list.
- With no selectors configured, all annotated elements should be treated as hidden; with all relevant selectors configured, all elements should appear.

## Why This Matters

Without this capability, teams cannot use a single proto definition to generate multiple audience-specific API documents (for example, one for public consumers and one for internal use). Every internal detail leaks into every generated spec, which is both a documentation quality problem and a potential security concern.
