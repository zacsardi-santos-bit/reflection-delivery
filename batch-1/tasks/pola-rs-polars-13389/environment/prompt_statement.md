I'm working with binary data in SQL queries and running into several missing features that I'd expect to find in a standard SQL implementation.

First, there's no way to write binary data literals inline in SQL. I need to be able to express byte values using bit-pattern notation (strings of zeros and ones) and hexadecimal notation directly in SELECT columns and WHERE clause filters against binary-typed columns. When I write an invalid literal — like a bit pattern containing characters other than 0 or 1, or a hex string with an odd number of digits — I'd expect a clear error message, but right now none of this works at all.

Second, I noticed that common alternative names for the character-length function are not recognized, and there is no function to get the length of a string measured in bits rather than characters or bytes.

Third, a common plain-English word for the binary data type is not recognized as a type alias when casting columns, even though similar aliases are already supported.

Finally, it would help to be able to create an SQL context and register a placeholder empty table — right now passing in no data at all seems to not be supported when setting up the SQL context.
