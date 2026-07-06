# Extend Inefficient Dictionary Iteration Detection to Comprehensions

## Description

The rule that detects inefficient dictionary iteration currently only covers traditional for-loops. When a loop iterates over a dictionary's key-value pairs but only actually uses one of the two elements, the rule suggests using the more efficient keys-only or values-only iteration method instead.

However, this same inefficiency appears frequently in comprehension expressions — list comprehensions, set comprehensions, dictionary comprehensions, and generator expressions. Currently, none of these forms are checked, so developers miss out on optimization hints they would receive if they wrote the same logic as a for-loop.

## Expected Behavior

- A list comprehension, set comprehension, dict comprehension, or generator expression that unpacks both key and value from a dictionary's items but only uses the key should be flagged with a suggestion to iterate over just the keys instead.
- The same applies when only the value is used.
- Cases where the unused variable is a named identifier (not just a wildcard) that is simply never referenced in the body should also be detected.
- Comprehensions with multiple nested generators should have each generator checked independently.
- The rule should still allow cases where both key and value are genuinely used in the body or in a filter condition, and where the iteration target is not a two-element tuple.

## Why This Matters

Inconsistent lint coverage leads to developers optimizing for-loops but inadvertently leaving the same inefficiency in comprehensions and generators. Extending the rule to all iteration contexts ensures consistent feedback regardless of which style the developer prefers.
