Update the rust-analyzer syntax highlighting to correctly recognize and highlight numeric tuple field indices when they appear inside macro invocations. Ensure that the syntax highlighting snapshot reflects the correct expected output for this case.

*   Ensure that numeric tuple field accesses inside macro arguments are highlighted as field accesses, not unresolved references.
*   Update the syntax highlighting snapshot file located at `crates/ide/src/syntax_highlighting/test_data/highlight_macros.html`:
    *   Modify the HTML output for the test input involving a tuple field access expression inside a format macro argument.
    *   Ensure the tuple field access expression `(92,).0` inside the `format_args!` call produces HTML with:
        *   Opening parenthesis with class `parenthesis macro`
        *   Number `92` with class `numeric_literal macro`
        *   Comma with class `comma macro`
        *   Closing parenthesis with class `parenthesis macro`
        *   Dot operator with class `operator macro`
        *   Tuple field index `0` with class `field library macro`
        *   Outer closing parenthesis with class `parenthesis macro`
*   Ensure the full expected HTML for the `format_args!` line in the updated snapshot is exactly:
    *   `<span class="macro default_library library">format_args</span><span class="macro_bang">!</span><span class="parenthesis macro">(</span><span class="string_literal macro">"Hello, </span><span class="format_specifier">{</span><span class="format_specifier">}</span><span class="string_literal macro">!"</span><span class="comma macro">,</span> <span class="parenthesis macro">(</span><span class="numeric_literal macro">92</span><span class="comma macro">,</span><span class="parenthesis macro">)</span><span class="operator macro">.</span><span class="field library macro">0</span><span class="parenthesis macro">)</span><span class="semicolon">;</span>`
    *   Ensure the tuple field index `0` has the CSS class `field library macro`, not `unresolved_reference macro` or `numeric_literal macro`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.