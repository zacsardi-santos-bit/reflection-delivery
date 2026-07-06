I'm working on a linter rule that checks whether React hooks declare all of their dependencies correctly. The rule already detects two kinds of problems: a dependency array that is missing required values, and a dependency array that includes extra or duplicate values. What it doesn't do yet is offer any automatic fix — it just reports the issue and leaves the developer to fix it manually.

I'd like the rule to provide unsafe auto-fix suggestions in both cases: one fix that adds missing dependencies to the array, and another that removes the unnecessary ones. The diagnostics should also be marked as fixable so tooling knows a fix is available.

There is also a related issue: when the rule's fix-validation logic runs against source code that already contains syntax errors, it panics. It should instead skip the bogus-node check and the re-parse validation whenever the original source was already malformed, rather than treating the modified output as the source of the problem.
