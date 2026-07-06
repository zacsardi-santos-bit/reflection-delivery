## Description

The API authentication configuration object has no way to indicate that an account is enrolled in a government-compliant cloud environment. This means that requests originating from such accounts are treated the same as any other account — they cannot be automatically routed to the specialized compliance infrastructure those accounts require.

## Expected Behavior

- The authentication provider struct should include a flag indicating whether the account is a government-compliance-enrolled account.
- When that flag is set, any outgoing API request authenticated with that provider should automatically include a special routing header so that backend infrastructure can route the request through the correct compliance path.
- When the flag is not set, no such routing header should be added.

## Why This Matters

Organizations operating under government compliance requirements must have their traffic routed through specific infrastructure. Without this flag and the corresponding header-injection behavior, those accounts cannot be distinguished from regular accounts, and their requests will be misrouted — potentially violating compliance requirements or causing request failures.
