I'm adding a pre-flight blocking check system to Storybook's CLI so we catch environment-level compatibility issues (wrong runtime version, that kind of thing) before an upgrade or init actually runs. Right now if someone's environment is incompatible the operation just fails partway through without telling them what went wrong, and there's no structured way to surface these blockers upfront.

I want two pieces here. First, a factory function for defining an individual blocker, where each blocker carries an identifier, an async check function that runs against the current environment, a user-facing message, and a log output string. Second, a main function that takes a list of these blockers, runs them, and handles whatever comes back.

The main function should behave like this: if no checks are passed in at all, it just returns nothing with no output. If all checks pass, it also returns nothing but tells the user no blockers were found. And if any checks fail, it returns the identifier of the first failing check, writes a log file summarizing the failures, and shows the user a prominent warning.

For the log file format, each failing blocker gets its identifier in parentheses followed by a colon, then the log output on the next line. When multiple blockers fail, separate their entries with a horizontal divider line so it's clear where one ends and the next begins.

The whole point is that people upgrading or setting up Storybook deserve to know immediately and clearly if their environment has problems, instead of wasting time on an operation that's doomed to fail, so the diagnostic output needs to be actionable.
