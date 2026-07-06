## Description

When dependency-cruiser computes reachability for modules and annotates each module with whether it is reachable from some origin, the resulting annotation only records *which rule* determined the reachability — not *which specific module* triggered it. This omission has two consequences:

1. **No traceability**: If multiple "from" modules can match the same rule, you can't tell which one caused a particular module to be marked reachable or unreachable.
2. **Capture groups don't work in reachability rules**: If a rule uses a regex capture group in the "from" path (e.g., to capture a folder name) and references that group in the "to" path (e.g., to scope the rule to that same folder), the capture group expansion doesn't happen — so the rule either matches nothing or matches too broadly.

## Expected Behavior

- Each reachability entry on a module should record the source path of the specific origin module that produced that annotation.
- Reachability rules that use regex capture groups in their "from" pattern should correctly expand those captured values into the "to" pattern — scoping each rule evaluation to the specific subtree associated with each origin module.
- When validating whether a rule is violated, the validator should use the recorded origin module path to expand any capture groups in the rule's "to" pattern, so that path-based conditions are evaluated correctly.

## Why This Matters

This makes it possible to write rules like "every module in a folder should be reachable from that folder's index file" — one rule that covers all folders, using capture groups to scope each check to its respective folder. Without this, you'd need to write a separate rule per folder, or accept false positives/negatives from overly broad rules.
