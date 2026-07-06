I'm working on improving the file permissions lint check in the linter.

*   In stable mode, the rule must flag a permission mask as dangerous when the statically-known set bits include any of: S_IWOTH (0o2) or S_IXGRP (0o10) — combined mask 0o12.

*   In preview mode, the rule must flag a permission mask as dangerous when the statically-known set bits include any of: S_IWOTH (0o2), S_IXGRP (0o10), S_IXOTH (0o1), or S_IWGRP (0o20) — combined mask 0o33.

*   For a bitwise OR expression, the statically-known set bits of the result must be the union of the set bits from both operands. If either operand has dangerous bits in its known-set, the overall expression must be flagged even when the other operand is an unknown variable.

*   For a bitwise AND expression, the statically-known set bits of the result must be the intersection of the set bits from both operands. An AND with an operand that has no dangerous bits in its known-set must be treated as safe regardless of the other operand.

*   For a bitwise XOR expression where both operands are structurally identical expressions with identical known-bit patterns, the result must be treated as exactly zero (no bits set, not oversized).

*   An integer literal that exceeds u64::MAX must be treated as 'oversized' and reported as an invalid mask.

*   An integer literal with any bit set above position 11 (i.e., outside the valid Unix permission range 0o7777) must be reported as an invalid mask.

*   For a bitwise OR expression, the oversized flag propagates if either operand is oversized. For a bitwise AND expression, the oversized flag is only set if both operands are oversized.

*   When an expression is both oversized and would otherwise be permissive, the rule must report it as invalid (not permissive).

*   The diagnostic message for an overly permissive mask must read: `os.chmod` setting a permissive mask `0o{N}` on file or directory, where N is the octal representation of the statically-known set bits.

*   The diagnostic message for an out-of-range or oversized mask must read: `os.chmod` setting an invalid mask on file or directory.

*   The preview-mode test for S103 must be registered in the flake8_bandit test module so that a preview-mode snapshot is generated and validated at crates/ruff_linter/src/rules/flake8_bandit/snapshots/ruff_linter__rules__flake8_bandit__tests__preview__S103_S103.py.snap.

*   The stable-mode snapshot at crates/ruff_linter/src/rules/flake8_bandit/snapshots/ruff_linter__rules__flake8_bandit__tests__S103_S103.py.snap must be updated to reflect the new test cases and their results.


*   Interface details: Type: Function
Name: is_s103_extended_dangerous_bits_enabled
Location: crates/ruff_linter/src/preview.rs
Signature: is_s103_extended_dangerous_bits_enabled(settings: &LinterSettings) -> bool
Description: Preview gate that returns true when preview mode is enabled, used to activate the expanded dangerous-bit mask for the S103 rule. Must be a pub(crate) const fn. Must be placed alongside the other preview gate functions in preview.rs.

Type: File (test registration)
Name: mod.rs (flake8_bandit test module)
Location: crates/ruff_linter/src/rules/flake8_bandit/mod.rs
Description: Rule::BadFilePermissions with Path::new("S103.py") must be added as a test_case attribute to the preview_rules test function. This registers the preview-mode snapshot test for S103, which will load the snapshot from ruff_linter__rules__flake8_bandit__tests__preview__S103_S103.py.snap.

Type: File (snapshot, updated)
Name: ruff_linter__rules__flake8_bandit__tests__S103_S103.py.snap
Location: crates/ruff_linter/src/rules/flake8_bandit/snapshots/ruff_linter__rules__flake8_bandit__tests__S103_S103.py.snap
Description: Existing stable-mode snapshot for the S103 fixture. Must be updated to reflect the context-line changes from the updated fixture comments and to include new diagnostics for the newly added fixture lines. The snapshot records which os.chmod calls produce diagnostics and what their messages are.

Type: File (snapshot, new)
Name: ruff_linter__rules__flake8_bandit__tests__preview__S103_S103.py.snap
Location: crates/ruff_linter/src/rules/flake8_bandit/snapshots/ruff_linter__rules__flake8_bandit__tests__preview__S103_S103.py.snap
Description: New preview-mode snapshot for the S103 fixture. Uses the Ruff preview diff format: shows linter.preview changing from disabled to enabled, a summary of removed/added diagnostics, and lists only the diagnostics that are added in preview mode compared to stable mode. Must include exactly 4 added diagnostics: the permissive mask errors for 0o664, 0o760, stat.S_IXOTH, and 0o21.

Type: Function
Name: bad_file_permissions
Location: crates/ruff_linter/src/rules/flake8_bandit/rules/bad_file_permissions.rs
Signature: bad_file_permissions(checker: &Checker, call: &ast::ExprCall)
Description: Main implementation of the S103 rule. Must be updated to use a KnownBits abstract interpretation instead of the previous exact-value-or-bail approach. Must consult is_s103_extended_dangerous_bits_enabled to choose between DANGEROUS_BITS_STABLE (0o12) and DANGEROUS_BITS_PREVIEW (0o33). Reports Invalid when known.oversized is true or known.ones has bits outside VALID_BITS (0o7777). Reports Permissive(known.ones) when known.ones intersects the dangerous mask. Takes no diagnostic action when the expression is entirely unknown (ones == 0, not oversized).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.