## Description

When upgrading or downgrading a Hudi table between older and newer table format versions, the handlers responsible for the transition do not fully migrate all configuration properties. Specifically, properties related to partition fields, record merge strategy, bootstrap index type, key generator type, and the initial table version marker are not being properly set or cleared during the transition. This can leave tables in an inconsistent configuration state after a version change.

## Expected Behavior

- When upgrading a table from the older format version to the newer one, the upgrade handler should populate the new version's configuration properties — including the partition fields, initial version marker, record merge mode, bootstrap index type, and key generator type — based on the existing table configuration.
- When downgrading a table from the newer format version back to the older one, the downgrade handler should remove version-specific properties (such as the initial version marker and record merge mode) from the table configuration and replace them with their equivalent older-format properties where applicable (e.g., restoring the legacy payload class name, removing the newer index and key generator type fields).

## Why This Matters

Without these property migrations, a table's configuration may contain stale, incorrect, or missing properties after a version transition, leading to unpredictable behavior when reading or writing data. Ensuring that both upgrade and downgrade paths correctly handle all relevant configuration properties is essential for safe and reliable version transitions.
