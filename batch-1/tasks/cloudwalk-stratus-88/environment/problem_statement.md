## Description

The Ethereum primitive types in this project each wrap a raw value and expose a set of conversion traits to interact with the rest of the system. The index type — used to represent ordered positions of items such as transactions and logs within a block — is currently missing a conversion to signed 32-bit integers.

This is a problem because the PostgreSQL storage backend stores transaction and log index values as 32-bit integer columns. Without a native conversion, saving block data to the database requires awkward, repetitive workarounds to extract the raw inner value before passing it to queries.

## Expected Behavior

- The index type should be convertible to a signed 32-bit integer using the same standard conversion pattern used by other primitives in this codebase.
- The conversion must preserve the value exactly: an index of 42 should become the integer 42.

## Why This Matters

Adding this conversion aligns the index type with the other Ethereum primitives that already support database-friendly numeric conversions, and removes the friction of manually unwrapping index values when persisting blockchain data to the database.
