## Description

The snapshot subsystem currently answers the question "is a full snapshot needed?" with a simple boolean. This means callers must interpret the answer themselves: false means "not full, so incremental." The API should be more explicit — instead of a yes/no answer, it should directly return *which type* of snapshot should be taken next.

Similarly, the constants used to classify snapshot kinds use lengthy prefixed names. These should be simplified to shorter, more idiomatic names that are easier to read and work with.

## Expected Behavior

- The method that currently returns a boolean indicating whether a full snapshot is needed should be replaced with one that returns the actual snapshot type due next (either full or incremental).
- The snapshot type constants should be renamed to use shorter, unqualified identifiers rather than their current prefixed forms.
- When the snapshot store is empty, the system should report that a full snapshot is due next.
- After a successful snapshot is persisted, the system should report that an incremental snapshot is due next.
- After a new database is loaded into the system, the system should report that a full snapshot is due next.
- After a vacuum operation (that does not require a full snapshot), the system should report that an incremental snapshot is due next.
- The companion method for explicitly marking that a full snapshot is needed should also be updated to accept the snapshot type directly, rather than being a dedicated "set full needed" function.

## Why This Matters

The boolean API creates ambiguity and puts the burden of interpretation on callers. A richer return type makes the intent explicit, avoids misinterpretation, and aligns the API with how the information is actually used — to decide what kind of snapshot to take next.
