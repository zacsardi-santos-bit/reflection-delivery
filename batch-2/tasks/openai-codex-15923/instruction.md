I have a Rust workspace where the logic for parsing and normalizing JSON schemas used by external tool definitions is buried inside the main core crate.

*   A new Rust crate named 'codex-tools' must be created at codex-rs/tools/ and registered as a workspace member in codex-rs/Cargo.toml. It must export parse_tool_input_schema, JsonSchema, and AdditionalProperties from its public API.

*   The parse_tool_input_schema function must accept a reference to a serde_json::Value and return Result<JsonSchema, serde_json::Error>. It must sanitize the schema before deserialization to normalize edge-case representations.

*   When parse_tool_input_schema receives a raw JSON boolean value (true or false), it must coerce it into JsonSchema::String { description: None }.

*   When parse_tool_input_schema receives a schema object that contains a 'properties' key but no explicit 'type' field, it must infer the type as 'object' and return a JsonSchema::Object variant with properties correctly parsed, required set to None if absent, and additional_properties set to None if absent.

*   When parse_tool_input_schema encounters a property with type 'integer', it must normalize it to the JsonSchema::Number variant (the Number variant must accept 'integer' as an alias for 'number').

*   When parse_tool_input_schema encounters an array schema that has no 'items' field, it must supply a default items schema of JsonSchema::String { description: None }.

*   When parse_tool_input_schema encounters an object schema whose 'additionalProperties' field is a non-boolean schema object, it must recursively sanitize that nested schema. Complex additionalProperties objects (with their own properties, required, etc.) must be parsed into AdditionalProperties::Schema(Box<JsonSchema>).

*   The AdditionalProperties enum must have two variants: Boolean(bool) for boolean additionalProperties values, and Schema(Box<JsonSchema>) for schema-object additionalProperties values. It must implement From<bool> and From<JsonSchema>.

*   The JsonSchema enum must use serde tag = 'type' with rename_all = 'lowercase', and must skip serializing Option fields when None. The Number variant must accept 'integer' as an alias via serde.


*   Interface details: Type: Function
Name: parse_tool_input_schema
Location: codex-rs/tools/src/json_schema.rs
Signature: parse_tool_input_schema(input_schema: &serde_json::Value) -> Result<JsonSchema, serde_json::Error>
Description: Parses and normalizes a raw JSON value representing a tool input schema into the internal JsonSchema type. Internally sanitizes the schema before deserialization to handle missing `type` fields, boolean schema shorthand, integer type aliases, missing array items, and complex additionalProperties schemas.

Type: Enum
Name: JsonSchema
Location: codex-rs/tools/src/json_schema.rs
Description: An internal enum representing a supported subset of JSON Schema. Variants: Boolean { description: Option<String> }, String { description: Option<String> }, Number { description: Option<String> } (also aliased from "integer"), Array { items: Box<JsonSchema>, description: Option<String> }, Object { properties: BTreeMap<String, JsonSchema>, required: Option<Vec<String>>, additional_properties: Option<AdditionalProperties> }. Derives Debug, Clone, Serialize, Deserialize, PartialEq. Uses serde tag = "type", rename_all = "lowercase".

Type: Enum
Name: AdditionalProperties
Location: codex-rs/tools/src/json_schema.rs
Description: Represents the additionalProperties field of a JSON Schema object. Variants: Boolean(bool), Schema(Box<JsonSchema>). Derives Debug, Clone, Serialize, Deserialize, PartialEq. Uses serde untagged.

Type: Module (lib.rs re-exports)
Name: codex-tools crate public API
Location: codex-rs/tools/src/lib.rs
Description: The lib.rs file must re-export AdditionalProperties, JsonSchema, and parse_tool_input_schema from the json_schema module. The crate must be registered in the workspace (codex-rs/Cargo.toml) as a member at path "tools" with the package name "codex-tools".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.