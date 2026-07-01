I'm working on Storybook's CLI and I need to add a pre-flight blocking check system. The idea is that before an upgrade or initialization proceeds, we should run a set of compatibility checks and, if any of them fail, halt the process and tell the user clearly what's wrong.

I need to implement two things: a factory function for defining individual blockers (each with an identifier, an async check, a user-facing message, and a log string), and a main function that takes a list of these blockers, runs them, and handles the results.

The main function should behave like this:
- If no checks are given, just return nothing without any output.
- If all checks pass, return nothing but tell the user no blockers were found.
- If any checks fail, return the identifier of the first failed check, write a log file summarizing each failure, and display a prominent warning to the user.

The log file format should list each failing blocker with its identifier in parentheses followed by a colon, then the log output on the next line. When multiple blockers fail, their entries should be separated by a horizontal divider line.
