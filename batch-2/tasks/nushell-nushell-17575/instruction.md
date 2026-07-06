I'm running into an issue with how nushell describes the type of a table when columns contain values of different types.

*   When the describe command is run on a table where a column contains mixed types, the resulting type description must never contain nested union types — the output string must never include the substring 'oneof<oneof<'.

*   When a table has a 'content' column where some rows contain a simple any/string value and other rows contain a nested record (such as a record with a 'content' key holding a list), the describe output must be a flat union. Specifically, the input '[ ...(0..4 | each { {content:[{content:[any]}]} }), {content:[any]} ] | describe' must produce exactly 'table<content: list<oneof<string, record<content: list<string>>>>>>'.

*   The flattening of nested union types must work regardless of the order in which differently-typed rows appear. When the simpler-typed row comes first and more complex-typed rows follow, the describe output must still contain the flattened union 'table<content: list<oneof<string, record<content: list<string>>' and must not contain 'oneof<oneof<'.

*   Glob-typed values and string-typed values in the same table column must be kept as distinct types and must not be collapsed into a single string type. When glob appears before string in the column, the output must contain 'table<content: oneof<glob, string>>'. When string appears before glob, the output must contain 'table<content: oneof<string, glob>>'. In neither case should the output contain 'oneof<oneof<'.

*   The order of types within a union type in the describe output must reflect the order in which distinct types are first encountered across the table rows.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.