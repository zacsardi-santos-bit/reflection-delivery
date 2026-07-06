## Description

Route configurations in the Konnect integration can contain template placeholder expressions — syntax used by Insomnia's environment variable system to inject dynamic values. When these routes are published or synced to external infrastructure, those template expressions remain in the route data as raw strings. This means sensitive variable names or configuration details may leak to external services that have no concept of the template language, and the expressions may also cause unexpected parsing or processing failures on the receiving end.

## Expected Behavior

- Before a route is sent externally, any template expression syntax should be stripped from the route's string fields (name, paths, hosts, methods, headers, and expression).
- Stripping should remove only the template expression itself, leaving any surrounding text intact.
- Array fields like paths and hosts should have each entry sanitized individually, keeping partial values.
- Array fields like methods should have fully-stripped entries removed entirely; if no entries remain, the field should fall back to null so that a default can apply.
- Header entries whose key or value is entirely a template expression should be dropped completely after sanitization.
- Both variable interpolation syntax and block tag syntax should be stripped.
- Template injection via interleaved or nested delimiter combinations should also be neutralized.
- Unpaired delimiters that do not form a complete template expression should be left unchanged.
- Fields that are already null should be handled gracefully without errors.

## Why This Matters

Without sanitization, any route that references environment variables via template syntax would expose those variable names to external services in plain text. This is both a potential security concern and a correctness issue, since the external service cannot render the templates and may reject or mishandle the route configuration.
