## Description

Add a new lint rule to the JavaScript analyzer that detects "yoda expressions" — comparisons written with a constant value on the left side of the operator, such as a number, string, boolean, or null appearing before the variable being tested.

This ordering is unusual and can be harder to read. Most developers expect comparisons to be written with the variable first and the constant second — placing the variable on the left side and the literal on the right. Enforcing consistent ordering improves readability.

## Expected Behavior

- The rule should flag any comparison expression where a literal constant appears on the left side of the operator and a variable or non-literal expression appears on the right side.
- When the rule fires, it should offer an automatic safe fix that flips the operands into the conventional order, correctly inverting the comparison direction when needed (e.g., a "less than" operator becomes "greater than" after swapping).
- The fix should work correctly with complex expressions including parenthesized operands, inline comments, negated values, yield expressions, await expressions, and assignment expressions.
- The rule should NOT flag comparisons where both sides are constants, or comparisons that use non-comparison operators (like bitwise operators).
- The rule should NOT flag range-check patterns where two comparisons are combined to test whether a variable falls within a lower and upper bound. These idiomatic patterns are still readable even when a literal appears on the left side.

## Why This Matters

Consistent comparison ordering is a common code style guideline, and automated detection makes it easy to enforce without manual code review. The auto-fix capability means developers can correct all instances in their codebase with minimal effort.
