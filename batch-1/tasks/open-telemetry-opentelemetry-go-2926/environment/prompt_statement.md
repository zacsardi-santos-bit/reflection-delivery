I'm working on the metrics SDK and need to implement a "view" system that lets users customize how instruments are matched and transformed before their data is exported. Right now there's no way to select a specific instrument by name, rename it, update its description, or restrict which attributes it reports — I need to build this from scratch as a new package.

The view needs to support matching instruments by name using wildcard patterns: one wildcard character type should match exactly one character, and another should match zero or more characters. Special characters that have meaning in regular expressions should be treated as plain literals in these name patterns. Matching should also support filtering by instrumentation library scope, where only non-empty fields in the scope need to match.

Once matched, the view should be able to rename the instrument and/or override its description. It should also support filtering the attribute set down to a specified list of keys, passing all attributes through unchanged when no filter is set.

Creating a view with no match criteria at all should fail with an error. Renaming should also be disallowed when the name pattern is a wildcard (since renaming applies to a specific instrument, not to all instruments matching a broad pattern) — this should also return an error.

The package needs to expose a constructor that takes functional options, along with option functions for instrument name matching (with wildcard support), instrumentation scope matching, renaming, description overriding, and attribute filtering.
