## Description

The Cloudflare Terraform provider migration tool has several bugs that prevent it from correctly handling real-world load balancer configurations during migration from older to newer provider versions.

## Problems

**Nested block transformation is incomplete.** When a load balancer resource contains routing rules, each rule often has an override block that itself contains multiple regional pool assignments (region pools, country pools, pop pools). The migration tool converts the outer rules correctly but silently drops or corrupts the nested pool assignment blocks. Users lose their regional routing configuration after running the migration.

**Multiple instances of the same block type are collapsed.** When several identical block types appear inside an override block (for example, four separate region pool entries covering different geographic regions), the tool should preserve all four entries as distinct items. Instead, it merges them or only retains one, causing routing rules to silently change behavior.

**Three known edge cases in rules migration were disabled.** The following scenarios were not handled and had their tests disabled:
- Rules that were already partially migrated (where the region field already contains a list) would be incorrectly re-processed.
- An empty rules list would cause an error instead of being preserved as-is.
- Rules that have no overrides sub-block would cause an error instead of being preserved as-is.

**Output format for lists of objects is too verbose.** The migration tool produces an expanded multi-line bracket format for lists of objects, but the expected compact format places the opening brace on the same line as the opening bracket, and separates consecutive objects inline.

**Load balancer pool origin header format is wrong.** The tool migrates the header attribute inside origin blocks to an intermediate format rather than the correct compact single-key format. Configurations with static origins and those using dynamic iterator-based definitions are both affected.

## Expected Behavior

- Nested override blocks with multiple regional pool assignments should be fully preserved after migration, with all entries intact.
- Lists of objects should use compact inline formatting.
- The origin header attribute should use the correct simplified key format.
- All three previously-disabled edge cases (already-migrated region lists, empty rules, rules without overrides) should be handled correctly.

## Why This Matters

Users running the migration tool on production Cloudflare Terraform configurations expect a safe, lossless conversion. Silent data loss in routing rules or header configuration can cause unexpected traffic routing changes that are difficult to detect until production issues arise.
