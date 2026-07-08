I'm getting bogus lint warnings from the rule that flags unnecessary rounding of integer values, it's way too aggressive right now. The problem is it flags rounding calls even when the precision argument isn't knowable at analysis time, so things like variables, arithmetic expressions, or values that could be negative all get flagged even though the rounding might genuinely change the result there.

The specific cases I keep hitting: when I pass a negative precision, the round call actually changes the integer (rounding to the nearest ten, hundred, etc.), so that's not unnecessary and shouldn't be flagged. Same deal when the precision is a variable or a computed expression, the rule can't statically know whether it's a no-op, so it needs to stay quiet in those cases too.

What I want is for the rule to only warn when it's completely certain the rounding has no effect. That means: no precision argument given at all, precision explicitly set to none/absent, or a non-negative integer literal precision. Those are the only provably-unnecessary cases. Everything else it should leave alone.

The reason this matters is the false positives suggest removing rounding that actually does something, and if someone applies the fix it's a silent correctness bug. So keep it conservative, 100% certain or nothing.
