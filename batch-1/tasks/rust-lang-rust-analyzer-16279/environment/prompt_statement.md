I've noticed that rust-analyzer's syntax highlighting is incorrectly treating numeric tuple field accesses as unresolved references when they appear inside macro arguments. For example, if I access a field of an inline tuple using a numeric index and pass that expression as an argument to a format-string macro, the numeric index gets highlighted in the error color with a wavy underline — even though the code is completely valid Rust.

This is confusing because it makes perfectly correct code look broken in the editor. The numeric index in a tuple field access should be correctly recognized as a field access and highlighted accordingly, not flagged as an unresolved reference.

I'd like rust-analyzer to correctly recognize and highlight these numeric tuple field indices when they appear inside macro invocations. The test snapshot for the macro highlighting test should also be updated to reflect the correct expected output for this case.
