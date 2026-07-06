# Add a Rich Header List API to Request and Response Objects

## Description

Currently, when writing pre-request scripts or test scripts in Bruno, the only way to access or modify HTTP headers is through low-level raw object access or a small handful of individual getter/setter methods on the request object. There is no structured, consistent API for working with headers as a list — operations like searching for a header by predicate, iterating over all headers, or checking whether a header is present with a specific value require awkward workarounds.

This means scripts that need to inspect or manipulate headers in any non-trivial way end up being verbose and fragile. It is also difficult to work with "disabled" headers (headers that are defined in the collection but toggled off) since the raw object access does not expose them.

## Expected Behavior

- Both the request object and the response object should expose a header list property with a rich, consistent interface.
- The header list should support read methods (get by key, get full object, get all, get by index, count), search methods (has, find, filter), iteration methods (forEach, map, reduce), and transform methods (to plain object, to string, to JSON).
- All key-based lookups should be case-insensitive, matching HTTP header semantics.
- The request's header list should be fully mutable: headers can be appended, set, deleted, cleared, populated in bulk, or merged from another source.
- The response's header list should be read-only; attempting to modify it should result in a clear error.
- Disabled headers (headers toggled off in the UI) should be visible through the header list, marked as such, and included in counts and searches.
- Iteration methods should accept an optional context object for controlling the execution context within callback functions.

## Why This Matters

This improves the scripting experience significantly: developers can write cleaner, more expressive scripts that interact with headers in a familiar, well-defined way. It also enables consistent behavior when scripts are converted between Bruno and Postman formats — header list operations should be translated correctly in both directions so that collections can be imported and exported without losing header manipulation logic.
