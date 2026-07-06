Enhance the Biome CSS parser and linter to support two modern CSS at-rules: `@position-try` and `@view-transition`. Ensure that the parser correctly builds syntax trees for these at-rules and that the linter recognizes them as valid, preventing false-positive warnings.

*   Update the CSS lexer to recognize new keyword tokens:
    *   Map `b"position-try"` to `POSITION_TRY_KW`.
    *   Map `b"view-transition"` to `VIEW_TRANSITION_KW`.

*   Modify the CSS parser to support new at-rules:
    *   Implement `@position-try` at-rule parsing:
        *   Structure: keyword, dashed custom identifier, CSS declaration block.
        *   Produce `CssPositionTryAtRule` AST node with fields: `position_try_token`, `name`, and `block`.
        *   Handle missing/malformed identifiers by producing `CSS_BOGUS_AT_RULE`.
    *   Implement `@view-transition` at-rule parsing:
        *   Structure: keyword, CSS declaration block.
        *   Produce `CssViewTransitionAtRule` AST node with fields: `view_transition_token` and `block`.
        *   Produce `CSS_BOGUS_AT_RULE` if no opening curly brace is found.
    *   Integrate both at-rules into `AnyCssAtRule` enum:
        *   Add variants `CssPositionTryAtRule(CssPositionTryAtRule)` and `CssViewTransitionAtRule(CssViewTransitionAtRule)`.

*   Ensure declaration blocks for both at-rules can be empty or contain standard CSS properties, matching snapshot outputs.

*   Update `CssSyntaxKind` with new syntax kind variants:
    *   `POSITION_TRY_KW`, `VIEW_TRANSITION_KW`, `CSS_POSITION_TRY_AT_RULE`, `CSS_VIEW_TRANSITION_AT_RULE`.
    *   Extend `T![]` macro: `[position_try]` → `POSITION_TRY_KW`, `[view_transition]` → `VIEW_TRANSITION_KW`.

*   Implement parser functions:
    *   `parse_position_try_at_rule(p: &mut CssParser) -> ParsedSyntax` in `position_try.rs`.
    *   `is_at_position_try_at_rule(p: &mut CssParser) -> bool` in `position_try.rs`.
    *   `parse_view_transition_at_rule(p: &mut CssParser) -> ParsedSyntax` in `view_transition.rs`.
    *   `is_at_view_transition_at_rule(p: &mut CssParser) -> bool` in `view_transition.rs`.
    *   Update `parse_any_at_rule` dispatch to route:
        *   `T![position_try]` to `parse_position_try_at_rule(p)`.
        *   `T![view_transition]` to `parse_view_transition_at_rule(p)`.

*   Adjust the `noUnknownAtRule` lint rule:
    *   Recognize `position-try` and `view-transition` as known at-rule names.
    *   Ensure no diagnostic warnings are emitted for these at-rules.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.