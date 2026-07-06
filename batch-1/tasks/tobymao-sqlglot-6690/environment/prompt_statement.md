I'm running into a couple of problems with SQL transpilation involving time conversion functions in Snowflake.

First, when I write a time conversion call that uses a compact format string — one where the hours, minutes, and seconds are concatenated without any separator characters — the transpilation to DuckDB doesn't work. The same function call with a dotted or colon-separated format string transpiles fine, but the compact "no separator" style seems to be unrecognized. I'd also expect format strings to be normalized to a consistent case in the Snowflake output.

Second, I noticed that the safe/nullable variant of the time conversion function — the one designed to return null instead of throwing an error on bad input — is being transpiled to DuckDB using strict (non-nullable) parsing and casting operations. The safe variant should use error-tolerant equivalents for both the parsing step and the subsequent type cast, so that invalid inputs produce null rather than an exception. This was happening even for the simple case of using the safe variant without any format string.

Additionally, calling these time conversion functions with a format string should be correctly recognized as producing a time type when the system infers expression types.

It would be great to get these cases handled correctly so that Snowflake SQL using compact time formats and the safe variant of the function can be reliably transpiled to other SQL dialects.
