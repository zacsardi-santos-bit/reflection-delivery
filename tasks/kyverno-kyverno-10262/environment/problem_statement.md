## Description

When multiple policies or rules apply to the same resource kind in Kyverno's webhook configuration, the admission operation types (create, update, delete, connect) are not being accumulated correctly. Specifically, if one rule targets a resource without specifying any operations (meaning "all operations"), and another rule for the same resource specifies only a single explicit operation, the combined webhook configuration may end up missing operations — even though the first rule implicitly covers all of them.

This leads to webhooks that are silently under-configured, meaning some admission requests that should be intercepted and evaluated by the policy engine will pass through without being checked.

## Expected Behavior

- When combining rules (within a single policy or across multiple policies) that target the same resource kind, the resulting operation set should be the union of all operations from all matching rules.
- If any rule has no explicit operations listed (implying it should apply to all operations), the merged result must include the full default set of operations for that webhook type — all four operations for validating webhooks, and create/update for mutating webhooks.
- The accumulation must work correctly whether rules come from the same policy or from separate policies processed one at a time.

## Why This Matters

Incorrect operation merging can cause policies to silently not enforce on certain admission request types, creating security blind spots where resources can be created, updated, or deleted without being validated or mutated by rules that were intended to cover those operations.
