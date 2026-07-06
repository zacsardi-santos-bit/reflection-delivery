Implement support for Tencent's Hy3 model family in the tool-call parsing system. Ensure the system can automatically recognize and route Hy3 models to the appropriate tool parser by registering a named entry and model-name patterns.

*   Define a constant for the Hy3 model family:
    *   Add `HY_V3` as a public constant in the `names` module within `rust/src/chat/src/parser/tool/mod.rs`.
    *   Use the signature: `pub const HY_V3: &str = "hy_v3";`.
    *   Ensure it is included alongside existing constants like `HERMES` and `MINIMAX_M2`.

*   Update the `ToolParserFactory::new()` constructor:
    *   Modify the constructor in `rust/src/chat/src/parser/tool/mod.rs`.
    *   Register at least one model name pattern using `register_pattern`.
    *   Ensure the pattern matches model names containing "hy3" (case-insensitive).
    *   Map these patterns to `names::HY_V3`.
    *   Verify that `resolve_name_for_model("tencent/Hy3-preview")` returns `Some(names::HY_V3)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.