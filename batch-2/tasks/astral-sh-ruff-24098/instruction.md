I'm hitting a false positive in the duplicate union member lint rule when using f-strings with the self-documenting debug format (the "show variable name" syntax) inside type annotations.

*   The DebugText struct in crates/ruff_python_ast/src/nodes.rs must be refactored to include the expression source text alongside the leading and trailing text. The struct's debug output must show three fields in order: leading, expression, and trailing.

*   DebugText must provide a constructor DebugText::new(leading: &str, expression: &str, trailing: &str) -> Self that takes all three text components. The previously public fields leading and trailing must be replaced with private storage and public getter methods.

*   DebugText must expose getter methods leading() -> &str, expression() -> &str, and trailing() -> &str that return references to the respective text components.

*   The formatter normalizer in crates/ruff_python_formatter/tests/normalizer.rs must be updated to use debug.leading(), debug.expression(), and debug.trailing() accessor methods (not direct field access), and must reconstruct DebugText using ast::DebugText::new(&leading, &expression, &trailing).

*   The duplicate union member check (PYI016) must compare f-string literals by their full source text representation, including the expression source text and any debug specifier. Two f-strings that produce different runtime output must NOT be flagged as duplicates.

*   The following combinations must NOT be flagged as duplicate union members: typing.Literal[f"{x =}"] | typing.Literal[f"{x=}"] (different debug whitespace produces different output); typing.Literal[f"{0x0=}"] | typing.Literal[f"{0o0=}"] (different source notation); typing.Literal[f"{00=}"] | typing.Literal[f"{000=}"] (different source notation); typing.Literal[f"{x=}"] | typing.Literal[f"{x}"] (one has debug specifier, one does not); typing.Literal[f"{x:.2f}"] | typing.Literal[f"{x:.3f}"] (different format specs).

*   The following combinations MUST be flagged as duplicate union members: typing.Literal[f"{x=}"] | typing.Literal[f"{x=}"] and typing.Literal[f"{x}"] | typing.Literal[f"{x}"].


*   Interface details: Type: Struct
Name: DebugText
Location: crates/ruff_python_ast/src/nodes.rs
Description: Represents the debug text of a self-documenting f-string or t-string expression (e.g., f"{x=}"). Must store the expression source text alongside the leading and trailing text portions. The public fields leading and trailing that previously existed must be replaced by methods.
Signature:
  new(leading: &str, expression: &str, trailing: &str) -> Self
  leading(&self) -> &str
  expression(&self) -> &str
  trailing(&self) -> &str
  Debug output must display fields in this order: leading, expression, trailing


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.