Implement a package-level regex variable in the `river` package to enforce tag format constraints during job insertion. Ensure that the regex matches valid tag strings and rejects invalid ones based on the specified rules.

*   Define a package-level unexported variable named `tagRE` of type `*regexp.Regexp` in the `insert_opts.go` file within the `river` package.
    *   Ensure `tagRE` is accessible from test files within the same package.
*   Construct the `tagRE` regex to enforce the following tag format rules:
    *   Tags must be at least 3 characters long.
    *   Tags must start with a word character (letter, digit, or underscore).
    *   Tags must end with a word character (letter, digit, or underscore), not a hyphen.
    *   Tags may contain letters, digits, underscores, and hyphens in the middle.
    *   Tags must not contain commas or other special characters.
*   Validate the `tagRE` regex against example strings:
    *   Must match: 'aaa', '_aaa', 'aaa_', '777', 'my-tag', 'my_tag', 'my-longer-tag', 'my_longer_tag', 'My_Capitalized_Tag', 'ALL_CAPS', '1_2_3'.
    *   Must not match: 'a', 'aa', '-aaa', 'aaa-', 'special@characters$banned', 'commas,never,allowed'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.