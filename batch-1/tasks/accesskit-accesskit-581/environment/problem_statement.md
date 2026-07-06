## Description

The accessibility consumer library contains filtering logic that determines which nodes in an accessibility tree should be exposed to assistive technologies. This logic handles several important cases: normal nodes should be included, hidden nodes should exclude their entire subtree, focused nodes should be included even if hidden, certain structural container roles should be skipped without hiding their children, and internal text representation nodes should be excluded at the node level only.

However, there are currently no automated tests verifying these filtering rules. Without tests, regressions in this core logic could go undetected, potentially causing screen readers and other assistive technologies to receive incorrect node sets.

## Expected Behavior

The filtering functions should be tested to verify:
- A normal node is included in the accessibility tree
- A hidden node causes its entire subtree to be excluded
- A hidden node that holds focus is still included despite being hidden
- A generic container node is excluded at the node level (but not its children) by the standard filter, while the root-exception variant of the filter includes it
- When a parent is hidden, all children are also excluded via subtree exclusion — unless a child is focused, in which case the focused child is included
- Internal text run nodes are excluded at the node level only

## Why This Matters

These tests ensure that the core accessibility filtering logic is correct and that future changes cannot silently break the rules governing which nodes assistive technologies see. This is critical for accessibility correctness across all platforms that use this library.
