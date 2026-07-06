I'd like to add a JSON schema for yamlfmt configuration files to SchemaStore.

*   A new JSON schema file must be created at src/schemas/json/yamlfmt.json to validate yamlfmt configuration files.

*   The schema must accept the top-level field 'line_ending' with only the values 'lf' and 'crlf'; any other value (such as 'cr') must be rejected.

*   The schema must accept the top-level field 'match_type' with only the values 'standard', 'doublestar', and 'gitignore'; any other value (such as 'glob') must be rejected.

*   The schema must accept the top-level field 'output_format' with only the values 'default', 'line', and 'gitlab'; any other value (such as 'compact') must be rejected.

*   The schema must accept the following top-level boolean fields: 'doublestar', 'continue_on_error', 'gitignore_excludes'.

*   The schema must accept the following top-level string array fields: 'include', 'exclude', 'regex_exclude', 'extensions'.

*   The schema must accept a top-level string field 'gitignore_path'.

*   The schema must accept a top-level 'formatter' object that can be either a basic formatter configuration or a kyaml formatter configuration.

*   The basic formatter (either explicit with type 'basic', or implicit when no type is specified) must accept these boolean fields: 'include_document_start', 'retain_line_breaks', 'retain_line_breaks_single', 'disallow_anchors', 'scan_folded_as_literal', 'indentless_arrays', 'drop_merge_tag', 'trim_trailing_whitespace', 'eof_newline', 'strip_directives', 'indent_root_array', 'disable_alias_key_correction'.

*   The basic formatter must accept integer fields 'indent', 'max_line_length', 'pad_line_comments', and 'array_indent'.

*   The basic formatter's 'force_array_style' field must only accept the values '', 'flow', and 'block'; any other value (such as 'list') must be rejected.

*   The basic formatter's 'force_quote_style' field must only accept the values '', 'single', and 'double'; any other value (such as 'triple') must be rejected.

*   The kyaml formatter must only allow type set to 'kyaml' and no additional properties; using basic-formatter-only options (such as 'include_document_start') alongside 'type: kyaml' must be rejected.


*   Interface details: Type: File
Name: yamlfmt.json
Location: src/schemas/json/yamlfmt.json
Description: A JSON Schema (draft-07 or compatible) that validates yamlfmt configuration files. The schema must define and enforce constraints for top-level configuration properties and formatter sub-properties as described below.

Top-level properties the schema must define:
- line_ending: string enum, valid values: "lf", "crlf"
- match_type: string enum, valid values: "standard", "doublestar", "gitignore"
- output_format: string enum, valid values: "default", "line", "gitlab"
- doublestar: boolean
- continue_on_error: boolean
- gitignore_excludes: boolean
- gitignore_path: string
- include: array of strings
- exclude: array of strings
- regex_exclude: array of strings
- extensions: array of strings
- formatter: object (oneOf basic formatter or kyaml formatter)

Basic formatter properties (used when formatter.type is "basic" or when type is omitted):
- type: string enum ["basic"] (optional, defaults to basic behavior)
- indent: integer
- include_document_start: boolean
- line_ending: string enum ["lf", "crlf"]
- retain_line_breaks: boolean
- retain_line_breaks_single: boolean
- disallow_anchors: boolean
- max_line_length: integer
- scan_folded_as_literal: boolean
- indentless_arrays: boolean
- drop_merge_tag: boolean
- pad_line_comments: integer
- trim_trailing_whitespace: boolean
- eof_newline: boolean
- strip_directives: boolean
- array_indent: integer
- indent_root_array: boolean
- disable_alias_key_correction: boolean
- force_array_style: string enum ["", "flow", "block"]
- force_quote_style: string enum ["", "single", "double"]

Kyaml formatter properties:
- type: must be the constant value "kyaml" (required)
- No additional properties are allowed beyond "type" — any extra property must cause validation failure


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.