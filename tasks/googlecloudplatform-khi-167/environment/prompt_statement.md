I'm working on the KHI project and need to add a new package that gives us a unified way to represent and traverse structured data as a typed node tree. Right now we don't have anything that lets us treat a YAML document, a JSON document, and an arbitrary Go value through the same API — each one requires its own handling.

I'd like a package that can convert both YAML/JSON text and arbitrary Go runtime values (scalars, slices, maps) into a tree of typed nodes. Each node should carry a type indicator — scalar, sequence, or map — and let you read its scalar value or iterate over its children. When iterating children, each entry should carry both a positional index and, for maps, the string key. Map keys should be orderable alphabetically to guarantee deterministic traversal.

The package should also include a utility for building dotted path strings that properly escapes dots appearing inside individual key segments so there's no ambiguity between path separators and literal dots in key names.

Both YAML and JSON inputs should be handled by the same parsing function since YAML is a superset of JSON. Scalar values parsed from YAML should be typed correctly: null becomes a nil value, booleans stay booleans, integers stay integers, floats stay floats, and timestamps become time values.
