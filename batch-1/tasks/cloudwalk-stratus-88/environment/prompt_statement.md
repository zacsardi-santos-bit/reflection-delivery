I'm working on an Ethereum node implementation in Rust that defines a set of primitive types for representing blockchain data. One of these types represents an ordered index — used to track the position of transactions and logs within a block. I need to add support for converting this index type into a signed 32-bit integer, because the database layer (PostgreSQL) stores those positions as 32-bit integer columns.

Right now, the index type can be converted to several other numeric representations, but not to a signed 32-bit integer. This means that whenever I try to pass a transaction or log index into a database query, I have to manually unwrap the inner value instead of using a clean, idiomatic conversion like the rest of the codebase does.

Can you add the missing conversion so that an index value can be turned into a signed 32-bit integer that preserves the original value?
