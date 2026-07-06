## Description

IP pool validation logic is currently duplicated across multiple packages and produces inconsistent error messages. When IP ranges overlap, the error messages differ between the IPAM and external IP pool controllers — some messages say "overlap" without identifying which specific ranges conflict, some reference "pool" while others would be clearer saying the full resource type name. Additionally, when a range falls outside of the configured subnet, the error messages from the two controllers differ in wording despite expressing the same constraint. This inconsistency makes it harder for operators to understand what went wrong.

There is also a gap in the restriction policy: existing IP ranges in a pool can currently be updated (changed) even though this should not be allowed for the same reasons that deletion is not allowed. The error message only mentions deletion, which is misleading.

## Expected Behavior

- All IP range validation logic (parsing CIDRs, parsing start/end ranges, checking overlap, validating against subnet info) should be consolidated into a shared, reusable location.
- Overlap error messages should clearly identify both conflicting ranges and, when relevant, the full resource type and name of the other pool.
- The error message for restricting changes to existing IP ranges should cover both updates and deletions.
- Subnet containment errors should use a consistent format: the range and the subnet should both be identified.
- Prefix length validation errors should use a consistent lowercase format.

## Why This Matters

Consistent and clear error messages allow cluster operators to quickly diagnose misconfigured IP pools. Centralizing the validation logic reduces the chance of inconsistencies creeping back in as both controllers evolve independently.
