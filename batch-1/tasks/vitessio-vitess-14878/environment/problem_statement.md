## Description

The constants and types used to represent MySQL server capabilities (such as support for instant DDL, fast table drops, JSON columns, or various version-gated features) are currently defined inside the core MySQL client package. Any package that needs to check whether a MySQL server supports a particular feature must import the entire MySQL connection package, even when it only needs access to these simple constants and the associated function type. This creates unnecessary coupling across the codebase.

## Expected Behavior

- MySQL server capability constants and the associated function type should be extracted into a dedicated, self-contained sub-package so they can be imported independently without pulling in the full MySQL client.
- All existing references across the codebase should be updated to use the new package location.
- The schema comparison library should gain new functionality to determine whether a given table alteration can be applied using the instant DDL mechanism — which avoids full table rebuilds on supported MySQL versions. This check should inspect the specific alter operations being performed (adding or dropping columns, changing column defaults, extending enum/set values) against the server's reported capabilities.
- The schema diff type should expose a method to determine whether an entire set of schema changes can be applied instantaneously across all contained diffs.

## Why This Matters

Schema migration tooling needs to know upfront whether a particular change can be applied as an instant operation rather than triggering a full table rebuild. Without capability abstractions accessible independently of the MySQL connection layer, and without a function in the schema comparison library to evaluate instant DDL eligibility, migration tools must either duplicate this logic or accept the full import weight of the MySQL package. Centralizing these checks in the right packages improves modularity and provides a reusable, well-tested decision function.
