## Description

CSS gradient functions have a strict direction syntax that has evolved over time, and it is easy to accidentally write a direction value that looks plausible but is actually non-standard. For example, older browser documentation and tutorials show bare directional keywords as the first argument to a gradient function, but the current specification requires those keywords to be preceded by a direction-indicator prefix. Similarly, bare numeric angle values without a unit suffix are not valid. To make things more complex, the vendor-prefixed variants of the gradient function (those intended for older browser engines) actually use the old syntax — so using the prefixed form inside a vendor-prefixed gradient is the mistake, while the bare keyword form is correct there.

## Expected Behavior

- A lint rule should flag gradient functions that use bare cardinal directional keywords without the required direction-indicator prefix in the standard function form.
- The rule should flag angle values that are bare numbers without a recognized unit suffix.
- The rule should flag invalid or malformed direction strings, including those that duplicate a keyword.
- For vendor-prefixed gradient functions, the rule should flag directions that incorrectly use the direction-indicator prefix, since those functions expect the older bare-keyword form.
- The rule should not flag valid direction syntax such as properly prefixed directional phrases, angles with a recognized unit suffix, or gradient functions whose first argument is a color rather than a direction.
- Color interpolation syntax appearing in the direction should not be treated as an error.
- The rule should apply consistently regardless of the function name's letter casing.

## Why This Matters

Incorrect gradient direction syntax is a silent bug — the CSS may be accepted by the browser's parser but render the gradient in the wrong direction, or be ignored entirely. Having a lint rule that catches these mistakes at development time prevents visual regressions and ensures the code follows the specification.
