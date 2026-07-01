Fix the TypeScript generation in your Rust library to ensure correct quoting of tag keys and proper handling of nested tagged enums. Address the issues with unquoted tag keys and incorrect intersection types for flattened enums.

*   Ensure tag attribute keys in generated TypeScript are quoted:
    *   When a Rust type has a tag attribute (e.g., `#[ts(tag = "...")]`), the tag field key in TypeScript must be a quoted string literal.
    *   Example: A struct with a tag attribute 'type' should produce `{"type": "StructName", a: number, b: number}` in TypeScript.

*   Correctly format internally-tagged enum variants:
    *   If all named fields are skipped, ensure exactly one space before the closing brace in TypeScript output.
    *   Example: Output should be `{"tag": "VariantName", }`.

*   Implement intersection types for flattened enums:
    *   When an internally-tagged enum variant flattens another internally-tagged enum, generate an intersection type.
    *   Format: `{"own_fields"} & ({"inner_enum_variants"})`.

*   Match struct and enum TypeScript declarations:
    *   When a struct flattens an enum type, its TypeScript declaration must match the flattened enum's declaration.

*   Generate specific TypeScript for complex enums:
    *   For an enum with 'Input' and 'Label' variants, ensure the TypeScript output matches:
        *   `{"type": "Label", text: string}`
        *   `{"type": "Input", name: string | null, placeholder: string | null, default: string | null} & ({"input_type": "Text"} | {"input_type": "Expression"} | {"input_type": "Number", min: number | null, max: number | null} | {"input_type": "Dropdown", options: Array<[string, string]>})`

*   Ensure the full declaration for complex flattened types matches:
    *   `type InputFieldElement = {"type": "Label", text: string} | {"type": "Input", name: string | null, placeholder: string | null, default: string | null} & ({"input_type": "Text"} | {"input_type": "Expression"} | {"input_type": "Number", min: number | null, max: number | null} | {"input_type": "Dropdown", options: Array<[string, string]>})`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.