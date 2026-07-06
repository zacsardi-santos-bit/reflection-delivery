## Description

When writing Gleam code that targets JavaScript, bit array patterns matching on large integer segments can silently produce incorrect results at runtime. JavaScript numbers can only accurately represent integers up to 52 bits, so if a developer writes a pattern that matches an integer segment larger than 52 bits, the matched value will be quietly truncated without any compile-time indication that something is wrong.

## Expected Behavior

- When a bit array pattern specifies an integer segment with a size greater than 52 bits (either as a direct literal size or via the size option syntax), the compiler should emit a warning targeted at the JavaScript platform
- The warning should clearly state the bit size of the segment, explain that JavaScript numbers are limited to 52 bits, and note that the value would be truncated
- The warning should suggest using the bytes segment option as an alternative
- Integer segments at or below 52 bits should not trigger this warning

Additionally, several existing warning messages have extra blank lines between the warning body text and the hint line that should be removed, and some existing warning body texts should be reflowed to better fit standard line widths.

## Why This Matters

Developers relying on large integer values in bit array destructuring on the JavaScript target can end up with silently wrong values that are extremely difficult to debug. Early detection at compile time with a clear, actionable warning saves significant debugging effort and prevents hard-to-trace runtime errors.
