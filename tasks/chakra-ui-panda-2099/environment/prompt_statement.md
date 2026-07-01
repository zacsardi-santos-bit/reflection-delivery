I'm working on adding container query support to the framework's pattern system. Right now, there's no built-in utility for creating a container query context — developers have to manually set the relevant CSS properties. I'd like a new built-in preset pattern that makes it easy to declare an element as a container, with optional name and type properties. The type should default to inline-size since that's the most common use case.

The pattern should work both as a regular function and as a JSX component. When you give the container a name, the framework's responsive shorthand syntax should generate the correct container-scoped media query in the CSS output — for example, using a container name in responsive styles should result in a properly scoped container query rule.

I also need the utility property types to include the container name property, typed to accept values from the container names token category (plus raw CSS values when not in strict mode).

One more thing: the at-rule sorting logic currently handles media queries but doesn't properly sort container query at-rules. Container queries should be sorted in the same way — non-container styles first, then min-width container queries in ascending order, then max-width queries. Right now the ordering of container queries in the generated CSS output is inconsistent.
