Implement support for an optional wildcard arm in the `match-template` macro to enhance its usability in real-world scenarios. Ensure the macro can handle either a single template arm or a template arm followed by a wildcard arm, preserving the latter in the expanded output.

*   Update the `MatchTemplate` struct in `components/match_template/src/lib.rs`:
    *   Add a field `wildcard_match_arm: Option<Arm>` to store the optional wildcard arm.
    *   Ensure the `Parse` implementation accepts 1 or 2 match arms:
        *   The first arm must be the template arm (a single pattern, no guard).
        *   The optional second arm must be a wildcard arm (pattern `_`, no guard).
        *   Reject inputs with 0 arms, more than 2 arms, or a non-wildcard second arm.

*   Modify the `expand` method on `MatchTemplate` in `components/match_template/src/lib.rs`:
    *   Signature: `expand(self) -> TokenStream`
    *   When `wildcard_match_arm` is `Some`, append it unchanged after all substituted arms in the output match expression.
    *   When `wildcard_match_arm` is `None`, produce output identical to the existing behavior, with only the substituted arms.

*   Ensure the `expand` method produces a `TokenStream` whose string representation matches a match expression with the substituted arms listed first, followed by the preserved wildcard arm if present.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.