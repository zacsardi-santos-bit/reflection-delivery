## Description

The zkSync commitment generator currently retrieves the events queue — a structured sequence of log records needed to compute cryptographic commitments for each L1 batch — exclusively from a dedicated database table. There is currently no way to derive this queue directly from the raw VM events that are also stored in the database. This creates a situation where the stored events queue cannot be independently verified or regenerated from first principles, and the system is unnecessarily coupled to a database table that may eventually be removed.

## Expected Behavior

- A new function should be available in the types library that accepts a list of raw VM events and converts them into the equivalent sequence of log query records.
- The conversion must handle events with varying numbers of indexed topics (one through four) and events whose data value spans multiple log records.
- The function should produce the same log query sequence that the commitment generator currently reads from the database, enabling a cross-check between the two sources.

## Why This Matters

Once this conversion function is in place, the commitment generator can verify that the computed events queue matches the stored one. In the future, the stored events queue table can be eliminated entirely and the events queue computed on the fly from the VM events, simplifying the data model and removing a point of redundancy. This is a foundational step toward making commitment generation more self-contained and maintainable.
