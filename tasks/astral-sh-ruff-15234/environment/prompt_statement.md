I'm seeing incorrect lint warnings from the rule that flags unnecessary rounding of integer values. The rule is too aggressive — it flags cases where the precision argument could actually change the result.

Specifically, when I pass a negative value as the precision, the rounding call genuinely changes the integer (rounding to the nearest ten, hundred, etc.), so it should not be flagged. Similarly, when the precision is a variable or a computed expression, the rule cannot know statically whether the rounding is a no-op, so it shouldn't flag those cases either.

The rule should only warn when it can be completely certain the rounding has no effect — that is, when no precision is given at all, when precision is explicitly "none," or when a non-negative integer literal precision is provided. In all other cases the rule should stay silent to avoid misleading suggestions that could break code.
