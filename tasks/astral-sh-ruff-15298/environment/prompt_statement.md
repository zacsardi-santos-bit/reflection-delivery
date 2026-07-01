I'm working with a lint rule that checks for missing explicit return statements in functions that declare a return type. The rule is supposed to be smart enough to not flag functions where a branch ends by calling another function that's annotated as never returning — because in that case, there's no need for an explicit return.

The issue is that the rule only recognizes the standard-library version of the "never returns" annotation. If someone uses the semantically equivalent annotation from a popular type extensions library, the rule incorrectly fires a warning even though the code is perfectly valid. I'd like both forms to be treated identically so that code using either annotation style doesn't get spurious warnings.

I also need to update the test fixture to reflect this — replacing the previous test cases (which used nested functions for this scenario) with top-level function examples, and adding equivalent cases for the type extensions annotation. The nested function case is a known limitation that isn't expected to work yet, so it should be documented as such in the fixture rather than being treated as a passing case.

Since the tests use snapshot testing, all the snapshot files for this rule family will need to be regenerated to match the updated fixture.
