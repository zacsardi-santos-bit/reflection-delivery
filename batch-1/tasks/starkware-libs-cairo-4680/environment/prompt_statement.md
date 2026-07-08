I'm hacking on the Cairo compiler and while loops are basically half-implemented right now. The parser and type checker handle them fine, but once we hit lowering they just bail with an unsupported error instead of emitting IR, so any Cairo program with a while loop can't compile even though it's semantically valid. I want to actually wire up the lowering so while loops go end-to-end like regular infinite loops already do.

The generated IR should evaluate the condition on each iteration and branch on it: if the condition's true, run the body then loop back around, and if it's false, exit the loop. Basically the classic condition-check-then-body-or-exit shape.

There's a related gap in the variable usage tracking too. The analysis that figures out which variables get read or written inside block-like constructs doesn't handle while loops properly yet. It needs to pull usage from both the condition expression and the body and aggregate them, then store that combined result keyed under the while expression itself, not just under the body block. Same idea for plain loop expressions, they should get their own usage entry that mirrors what the body uses.

And the usage reporting output is wrong on labels, it always prints the same generic label right now. It should actually say what kind of construct each entry is, whether it's a plain block, an infinite loop, or a while loop, so the report distinguishes them correctly when it dumps usage info.

The point here is parity: while loops are fundamental control flow and devs can't write condition-based iteration without this even though the syntax already parses. Getting lowering support in brings them up to the same level as the existing loop construct so those programs compile and run.
