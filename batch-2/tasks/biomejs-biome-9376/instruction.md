I'd like to add a new lint rule to Biome's nursery group that disallows identical titles in test cases and test group blocks within the same scope.

*   The rule must be registered in the nursery lint group under the identifier 'noIdenticalTestTitle', with the full diagnostic category path 'lint/nursery/noIdenticalTestTitle'.

*   When two test calls (using any of the recognized test function forms, including modifiers like .only or .skip) share the same static string title within the same scope (the same describe block or the top level), the second occurrence must be flagged with the main diagnostic message 'Duplicate test title found.', a note 'A test with this title already exists in the same scope.', and a further note 'Rename the test to give it a unique, descriptive title.'

*   When two describe-block calls share the same static string title within the same scope (the same parent describe block or the top level), the second occurrence must be flagged with the main diagnostic message 'Duplicate describe title found.', a note 'A describe with this title already exists in the same scope.', and a further note 'Rename the describe to give it a unique, descriptive title.'

*   Duplicate detection must be scoped per nesting level: the same title appearing in different sibling describe blocks (i.e., in different scopes) must NOT be flagged.

*   Test titles and describe titles must be tracked independently within a scope: a test and a describe block sharing the same name must NOT produce a diagnostic.

*   No-substitution template literals (template strings with no expressions) must be treated as static string titles and included in duplicate detection, in the same way as plain string literals.

*   Template literal titles that contain expression substitutions (dynamic titles) must NOT be flagged, as they cannot be statically compared.

*   The rule implementation file must be located at 'crates/biome_js_analyze/src/lint/nursery/no_identical_test_title.rs' and must declare a rule struct named 'NoIdenticalTestTitle'.

*   An options struct named 'NoIdenticalTestTitleOptions' (an empty struct with no fields) must be defined in 'crates/biome_rule_options/src/no_identical_test_title.rs' and exported from 'crates/biome_rule_options/src/lib.rs'.


*   Interface details: Type: Struct (Lint Rule)
Name: NoIdenticalTestTitle
Location: crates/biome_js_analyze/src/lint/nursery/no_identical_test_title.rs
Description: The main lint rule struct. Must be declared using the Biome `declare_lint_rule!` macro with name "noIdenticalTestTitle", language "js", in the nursery group. The rule must emit diagnostics matching the exact messages in the snapshot.

Diagnostic messages (must match exactly):
- Main message for duplicate test: "Duplicate test title found."
- Main message for duplicate describe: "Duplicate describe title found."
- Note 1 for test: "A test with this title already exists in the same scope."
- Note 1 for describe: "A describe with this title already exists in the same scope."
- Note 2 for test: "Rename the test to give it a unique, descriptive title."
- Note 2 for describe: "Rename the describe to give it a unique, descriptive title."

Type: Struct (Options)
Name: NoIdenticalTestTitleOptions
Location: crates/biome_rule_options/src/no_identical_test_title.rs
Description: An empty options struct (no fields) used as the rule's Options associated type. Must be derived with Default, Clone, Debug, Deserialize, Deserializable, Merge, Eq, PartialEq, Serialize, and optionally JsonSchema. Must be exported from crates/biome_rule_options/src/lib.rs as `pub mod no_identical_test_title;`.

Type: Registration
Name: lint/nursery/noIdenticalTestTitle
Location: crates/biome_diagnostics_categories/src/categories.rs
Description: The diagnostic category must be registered with the path "lint/nursery/noIdenticalTestTitle" and URL "https://biomejs.dev/linter/rules/no-identical-test-title".

Type: Registration
Name: NoIdenticalTestTitle (rule registration)
Location: crates/biome_configuration/src/analyzer/linter/rules.rs
Description: The rule name must be added to the RuleName enum as `NoIdenticalTestTitle`, mapped to the string "noIdenticalTestTitle", assigned to RuleGroup::Nursery, and included in the FromStr parse mapping.

Type: Registration (nursery mod)
Name: no_identical_test_title
Location: crates/biome_js_analyze/src/lint/nursery/mod.rs (or equivalent nursery module file)
Description: The module must be declared and the rule exported so it is included in the nursery group's rule set.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.