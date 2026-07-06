I'm using sqlc to generate Go code from my SQL queries, and I've run into a bug where optional named parameters that appear inside nested SQL function calls don't show up in the generated code at all.

Specifically, I have a MySQL query that filters rows using an optional parameter wrapped inside a fallback expression, which itself is nested inside a pattern-matching function call. When I run the code generator, the resulting parameter struct is completely empty — the parameters are just gone.

The same issue occurs with PostgreSQL. If the named parameter is nested more than one level deep inside SQL function calls, it doesn't get picked up.

This seems to happen specifically when the parameter appears as an argument to a function whose return type isn't a known table type or whose context prevents type inference through the outer function. The expected behavior is that the generated parameter struct should include all named parameters regardless of nesting, with types inferred from whatever context is available. For both MySQL and PostgreSQL, the generator should produce correct parameter structs with appropriately typed fields for these nested optional parameters.
