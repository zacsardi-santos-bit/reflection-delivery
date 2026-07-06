## Description

The conversation history summarization middleware currently only supports triggering on a single condition at a time — either a token threshold or a message count threshold. There is no way to require that multiple conditions all be satisfied simultaneously before summarization kicks in.

This is limiting because users often want to avoid unnecessary summarization when only one signal fires. For example, a developer might want summarization to occur only when the conversation is both very long in tokens AND has a large number of messages. Currently, this kind of composite logic cannot be expressed, and users must pick a single condition.

## Expected Behavior

- Users should be able to specify multiple conditions that must all be satisfied simultaneously (AND logic) before summarization triggers.
- Users should be able to specify multiple independent condition groups, where meeting any single group is sufficient (OR across groups).
- A list of groups may mix AND-style groups and single-condition entries.
- Existing single-condition configurations (both single conditions and lists of independent conditions) must continue to work exactly as before.
- An empty list of trigger conditions should result in summarization never being triggered.
- An empty AND-condition group should be rejected at configuration time, since it would vacuously match on every invocation.
- Invalid condition types, invalid metric names, out-of-range values, and non-numeric values for thresholds should be rejected at configuration time with clear error messages.
- Provider-reported token usage from the model should be honored as a source for satisfying token-based conditions within AND-style groups, not just for single-condition triggers.

## Why This Matters

Without the ability to combine conditions, users cannot fine-tune when summarization occurs and may experience either too-frequent or too-infrequent summarization. The AND/OR trigger logic gives users precise control over the conditions under which conversation history gets condensed.
