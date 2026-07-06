I'm working with a Rust enum library and I'd like a way to automatically derive a companion table type for my enums. The idea is that for any enum with unit variants, the macro generates a struct that holds one value of an arbitrary type per variant — basically a type-safe, enum-indexed map.

The generated table should support default construction, filling all slots with one value, constructing from a closure that gets called with each variant, and indexed read/write access using the enum itself as the key. I also want to be able to clone the table, transform it to a new value type using a closure, and have convenience methods that work with optional and result-typed slots — specifically, one that collects all option-typed values into a single optional table (returning nothing if any slot is missing), and one that collects all result-typed values into a single result (returning the first error if any slot failed).

Variants that are marked as disabled should be excluded from the table entirely — they should not appear as constructor parameters or slots — and trying to index with a disabled variant should panic. There's also a correctness requirement around enum variants whose names are reserved keywords; those need to work too.

The macro should be importable from the main library crate, not just the macros crate directly.
