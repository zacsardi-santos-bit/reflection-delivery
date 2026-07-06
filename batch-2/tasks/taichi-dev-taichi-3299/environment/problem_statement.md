I'm poking at the experimental AST refactorer in Taichi and hitting a bunch of gaps when I turn it on. First off, augmented assignment inside kernels is broken, so things like adding to a field in place, subtracting from it, or raising it to a power in place (the `+=`, `-=`, `**=` style ops) either blow up or give me the wrong answer. I need those to match what I'd get computing the op by hand. Ternary conditionals are also just not there, both the plain Python `a if cond else b` and the static variant, so I can't use them in kernels at all and I really want to.

Also static tuple unpacking doesn't work, I want to unpack multiple template args into separate static local vars in one go and right now that fails outright.

On error handling, two things are giving me opaque low-level assertion failures instead of clear messages. When I accidentally do a static assignment into an element of a static array, I should get a real syntax error telling me that's not permitted on array elements. And when I re-declare a variable that already exists via static assignment, I want a descriptive syntax error saying re-creating a variable isn't allowed, not some internal assert.

Last thing, and this one's annoying: kernel constructs only work if I import the module under its usual short alias. If I import it under some other name (totally valid Python) kernels quietly break even though the name shouldn't matter at all. Please make the refactorer resolve kernel constructs regardless of the import alias.

All of this needs fixing in the experimental refactorer itself so it handles these everyday Python patterns correctly. These are fundamental constructs people expect to work, and the better error messages make debugging way less painful.
