I'm working with the printf utility in a Rust reimplementation of the core Unix utilities and I've found two bugs in how it handles non-finite floating-point values (infinity and NaN) when format specifiers include field width or padding flags.

First, when I use a format that requests a leading space for positive values (the space-sign flag) along with a minimum field width, positive infinity and positive NaN don't get the expected leading space. The output ends up one character shorter than it should be, misaligned with the intended width.

Second, and more importantly, when I use zero-padding with a field width on non-finite values, they get padded with zeros instead of spaces. For example, formatting positive infinity in a zero-padded field of width 5 produces something like "00inf" rather than "  inf". According to POSIX and standard C printf behavior, zero-padding should be suppressed for non-finite values — spaces should always be used instead. The output for non-finite values with zero-padding should be identical to what you'd get with space-padding.

Both of these affect the formatting of infinity and NaN in contexts where developers rely on consistent column-aligned output.
