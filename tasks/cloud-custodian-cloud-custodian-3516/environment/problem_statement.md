## Description

When cloud custodian policies are triggered by DynamoDB table creation events, the policy needs to wait for the table to fully exist before applying actions — because newly created tables are not immediately accessible or taggable. However, this wait was not properly implemented as a separate mechanism, causing issues across several reported problems (#3361, #3171, #3514).

Additionally, when DynamoDB table resources are retrieved from the configuration history service rather than the live API, two problems occur:

1. The wait mechanism (intended only for newly-created live tables) is unnecessarily triggered for stable historical records that don't need it.
2. Encryption metadata fields in the configuration history format use different capitalization conventions than the live API, making the two sources produce incompatible resource representations that cause policies to behave inconsistently.

## Expected Behavior

- When a table-creation event triggers a policy, the system should wait for the table to become active before proceeding, using a configurable timeout.
- When fetching table data from the configuration history service, no waiting should occur.
- Encryption metadata fields retrieved from the configuration history service should be normalized to match the casing used by the live API, so the two data sources are interchangeable for policy evaluation.
- The configuration source should return a proper list of results rather than a lazy iterator, so downstream code can correctly measure and compare result sets.

## Why This Matters

Without these fixes, event-driven policies on newly created DynamoDB tables fail or produce errors because the table isn't ready. Meanwhile, configuration-rule based policies that inspect encryption settings may fail to find the expected fields, or unnecessarily incur delays from the table-availability wait.
