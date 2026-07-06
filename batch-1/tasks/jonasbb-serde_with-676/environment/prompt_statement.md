I'm using a Rust serialization helper library's wrapper types to control serialization behavior on my struct fields, and I'd like to also generate JSON schemas for those structs. The problem is that many of the wrappers I use don't support JSON schema generation yet — so when I try to derive JSON schema on a struct that uses these wrappers, the build fails.

I need JSON schema support added for the following wrappers: the byte-array wrapper, the null-defaulting wrapper, the separator-delimited string wrapper, type-conversion wrappers, map-from-sequence wrappers (for both vectors of pairs and fixed-size arrays of pairs), and the two set wrappers that handle duplicate entries differently.

For the set wrappers the schema semantics really matter: the one that silently accepts duplicates and keeps the last value should produce a schema that allows duplicates, while the one that rejects duplicates should produce a schema that requires unique items — meaning input with duplicate values should actually fail schema validation.

I'd also like a borrowed-string wrapper to produce a valid schema.
