Implement routing support for InternLM2 models in the Rust-based chat layer of vllm. Ensure models with identifiers containing "internlm2" are routed to the correct tool-call parser, while other models remain unaffected.

*   Define a constant `names::INTERNLM` with the value `"internlm"` in the `names` module located at `rust/src/chat/src/parser/tool/mod.rs`.
*   Update the tool parser factory's default pattern set:
    *   Register the substring `"internlm2"` to map to `names::INTERNLM`.
    *   Ensure model identifiers containing `"internlm2"` resolve to `Some(names::INTERNLM)`.
    *   Ensure model identifiers not containing `"internlm2"` resolve to `None`.
        *   This includes identifiers like `"internlm/internlm-chat-7b"`, `"internlm/internlm3-8b-instruct"`, `"internlm/Intern-S1"`, and `"internlm/Intern-S1-Pro"`.
*   Implement the `Internlm2ToolParser` struct in a new file at `rust/src/tool-parser/src/json/internlm2.rs`.
    *   Ensure it implements the `ToolParser` trait.
    *   Export it through the crate's public interface.
    *   Register it in the factory using `register_parser::<Internlm2ToolParser>(names::INTERNLM)`.
*   Update the error message for unknown tool parser names in `rust/src/chat/src/lib.rs`.
    *   Include `internlm` in the alphabetically sorted list of parsers.
    *   The expected string is: `"tool parser \`definitely_missing_tool_parser\` is not registered (choose from: deepseek_v3, deepseek_v31, deepseek_v32, deepseek_v4, gemma4, glm45, glm47, hermes, hy_v3, internlm, kimi_k2, llama3_json, llama4_json, minimax_m2, mistral, qwen3_coder, qwen3_xml)"`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.