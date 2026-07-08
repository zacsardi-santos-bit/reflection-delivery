I'm hitting a bunch of gaps in the SQL layer when I try to work with binary data and it's making the whole thing feel half-finished compared to real SQL dialects.

Biggest one: I can't write binary literals inline. I want bit-string notation, like a sequence of zeros and ones, that gets turned into the matching bytes following standard binary interpretation (MSB-first, zero-padded out to whole bytes), and I also want hex notation where pairs of hex digits become the raw bytes, case-insensitive so uppercase or lowercase both parse. Both of these need to work in SELECT expressions and in WHERE clause comparison filters against binary-typed columns, right now none of it works. And when the literal is bad I want a clear error, so a bit string containing anything other than 0 or 1 should be rejected with a message about non-binary characters, and a hex string with an odd number of digits should be rejected too since you can't make whole bytes out of that.

Also the string length functions feel incomplete. The common alternative names for the character-length function aren't recognized (they should just return character counts), and there's no way to get the length of a string in bits, so I want a bit-length function that returns byte count times 8.

Oh and casting: a plain-English word for the binary type isn't accepted as a type alias even though similar aliases already work, so that should resolve when I cast a column to it.

Last thing, smaller, I want to be able to spin up an SQL context and register a placeholder empty table with no data at all. Right now passing in nothing when setting up the context isn't supported and it blocks me from writing structural queries before I've got real data loaded.
