I'm running into a frustrating bug with assertion checking in promptfoo.

*   The handleContainsAny function must parse its string value using CSV-style quoting: fields wrapped in double quotes are treated as a single token even if they contain commas.

*   The handleContainsAny function must handle non-space whitespace (such as tabs) between comma-separated fields without producing spurious empty tokens.

*   The handleContainsAny function must support CSV-style doubled-quote escaping inside quoted fields: two consecutive double-quote characters inside a quoted field represent a single literal double-quote in the token value.

*   The handleContainsAny function must support backslash-escaped characters inside quoted fields: a backslash followed by a double-quote represents a literal double-quote, and a backslash followed by a backslash represents a literal backslash.

*   The handleContainsAny function must return pass: false (no false positives) when the output does not contain any of the correctly parsed tokens.

*   The handleIContainsAny function must apply all the same CSV-style quoting and escape rules as handleContainsAny but perform case-insensitive substring comparison. When the output does not match, it must return pass: false, score: 0, and a reason string in the format: 'Expected output to contain one of "<joined token list>"'.

*   The handleContainsAll function must parse its string value using the same CSV-style quoting rules (quoted fields preserve commas; backslash-escaped quotes and backslashes are unescaped), and return pass: true only when every parsed token is found in the output.

*   The handleIContainsAll function must apply all the same CSV-style quoting and escape rules as handleContainsAll but perform case-insensitive substring comparison. A quoted empty string (two consecutive double-quote characters as the entire value) must be treated as an empty token that always matches, since an empty string is contained in any string.


*   Interface details: Type: Function
Name: handleContainsAny
Location: src/assertions/contains.ts
Signature: handleContainsAny(params: AssertionParams) -> AssertionResult
Description: Checks whether the outputString contains at least one of the tokens parsed from the assertion value. Parses the value string using CSV-style quoting (double-quoted fields may contain commas; doubled quotes or backslash-escaped quotes inside a quoted field represent a literal quote; backslash-escaped backslashes represent a literal backslash; tabs between fields are treated as whitespace). Returns { pass: boolean, score: number, reason: string, assertion }.

Type: Function
Name: handleIContainsAny
Location: src/assertions/contains.ts
Signature: handleIContainsAny(params: AssertionParams) -> AssertionResult
Description: Case-insensitive variant of handleContainsAny. Applies the same CSV-style quoting parse rules to the value string and performs case-insensitive substring matching. When the output does not match, returns pass: false with reason in the format: 'Expected output to contain one of "<parsed tokens joined>"'.

Type: Function
Name: handleContainsAll
Location: src/assertions/contains.ts
Signature: handleContainsAll(params: AssertionParams) -> AssertionResult
Description: Checks whether the outputString contains every token parsed from the assertion value. Applies the same CSV-style quoting rules (quoted fields may contain commas; backslash-escaped quotes and backslashes are unescaped). Returns { pass: boolean, score: number, reason: string, assertion }.

Type: Function
Name: handleIContainsAll
Location: src/assertions/contains.ts
Signature: handleIContainsAll(params: AssertionParams) -> AssertionResult
Description: Case-insensitive variant of handleContainsAll. Applies the same CSV-style quoting parse rules and performs case-insensitive substring matching. An empty quoted string ('""') is treated as a token that always matches. Returns { pass: boolean, score: number, reason: string, assertion }.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.