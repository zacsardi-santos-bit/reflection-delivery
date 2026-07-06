## Description

The built-in string length function in the Pulumi Configuration Language (PCL) runtime produces incorrect results for non-ASCII text. It counts raw bytes or Unicode code points instead of user-visible characters (Unicode grapheme clusters). This means that emoji, accented characters, and composed sequences all report inflated lengths compared to what a human would expect.

For example, a family emoji composed of multiple joined code points should count as a single character, but currently reports as several. An emoji followed by a variation selector should count as one character, but currently counts as two. Regular ASCII strings are unaffected.

## Expected Behavior

- The string length function should return the number of Unicode grapheme clusters — the units that correspond to user-perceived characters.
- ASCII strings: length equals the character count (no change in behavior).
- Accented Latin characters: each base character plus its combining accent counts as one unit.
- Emoji with variation selectors: counts as one unit per visible glyph.
- ZWJ sequences (e.g., family emoji): the entire composed sequence counts as one unit.
- The split, join, and string interpolation operations should work correctly alongside the corrected length function.

## Why This Matters

Configuration programs using PCL that perform string length checks on non-ASCII input (such as user-provided names, labels, or tags with Unicode content) will silently get wrong answers. This can cause logic errors in programs that branch on or export string lengths. Correct grapheme cluster counting ensures the length function behaves consistently with what users expect from any modern string processing library.
