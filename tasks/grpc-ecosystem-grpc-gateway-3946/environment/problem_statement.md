## Description

When a request hits a gateway route that includes path parameters, the gateway should treat the path parameter value as authoritative. However, there is currently a bug where if the same request also includes a query parameter that targets the same field as the path parameter, the query parameter value silently overrides the intended path value — producing incorrect results.

This is particularly problematic because:
- Path parameters are part of the route definition and represent the canonical identity of a resource (e.g., a resource ID in the URL path)
- A caller could accidentally or maliciously provide a conflicting query parameter and receive unexpected behavior
- For nested message fields, the bug can also be triggered when a query parameter uses a different case convention than the field's proto name

## Expected Behavior

- When a URL path segment sets a field value (e.g., a resource identifier embedded in the path), any query parameter that targets that same field should be ignored. The path-derived value should always win.
- When a path parameter targets a nested message field, query parameters that address the same nested field — even if they use a different naming convention — should also be silently ignored.
- Query parameters for fields *not* set by path parameters should continue to be applied normally.

## Why This Matters

Developers relying on path parameters for resource identification expect those values to be authoritative. Allowing query parameters to silently override path parameters introduces a security and correctness gap: the correct resource is identified in the path, but the response may reflect a different (attacker-controlled or mistakenly provided) field value.
