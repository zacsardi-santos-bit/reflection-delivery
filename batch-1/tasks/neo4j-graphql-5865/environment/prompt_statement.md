I'm having trouble with default values in my Neo4j GraphQL schema. When I try to declare a default value on temporal fields like time-only types, local datetime, or date-only fields, it either fails validation or doesn't apply the default at runtime when I create nodes without supplying those fields. The same issue affects large integer fields — defaults don't seem to be supported there at all, even though I'd expect them to be.

On top of that, I've noticed that integer literals are being rejected as defaults for floating-point fields, which seems wrong since integers are valid float values. The same rejection happens with the coalesce directive on float fields.

Also, when I accidentally put a default value on a spatial/geographic field, the error message I get is confusing — it references an old set of supported types that doesn't match what actually works in practice.

I need the default value directive to properly validate and apply defaults at runtime for all temporal scalar types (including time with/without timezone, local datetime, and date-only types) and large integer fields. Integer literals should also be accepted as valid defaults for floating-point fields. And the error message for unsupported types (like spatial fields) should accurately list the types that are actually supported.
