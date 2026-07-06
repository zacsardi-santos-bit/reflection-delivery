Implement a new lint rule to detect unsupported ARIA attributes on HTML elements based on their implicit roles. Ensure the rule provides clear diagnostics and guidance to developers, while excluding custom components, elements with spread attributes, and elements with explicit presentation roles.

*   Implement the lint rule named `useAriaPropsSupportedByRole` in the file `crates/biome_js_analyze/src/lint/nursery/use_aria_props_supported_by_role.rs`.
    *   Use the `declare_lint_rule!` macro with the rule name "useAriaPropsSupportedByRole".
    *   Register the rule in the nursery lint group as "recommended: true" with version "next".
    *   Produce an error-level diagnostic message: "The ARIA attribute '{aria-attribute-name}' is not supported by this element."
    *   Include an informational note: "Ensure that ARIA attributes are valid for the role of the element."

*   Ensure the rule:
    *   Does not flag custom components, elements with spread attributes, or elements with explicit role='presentation'.
    *   Determines each element's implicit ARIA role using the `get_role_by_element_name` method.
    *   Checks ARIA attributes against the resolved role and flags unsupported attributes.

*   Define the `get_role_by_element_name` method in `crates/biome_aria/src/roles.rs`:
    *   Signature: `get_role_by_element_name(&self, element_name: &str, attributes: &FxHashMap<String, Vec<String>>) -> Option<&'static dyn AriaRoleDefinition>`
    *   Return the ARIA role based on the element name and attributes, considering explicit role attributes.

*   Update `crates/biome_js_analyze/src/lint/nursery.rs`:
    *   Declare the module with `pub mod use_aria_props_supported_by_role;`.
    *   Include the rule struct `UseAriaPropsSupportedByRole` in the `declare_lint_group!` macro.

*   Register the diagnostic category in `crates/biome_diagnostics_categories/src/categories.rs`:
    *   Name: `lint/nursery/useAriaPropsSupportedByRole`
    *   Documentation URL: "https://biomejs.dev/linter/rules/use-aria-props-supported-by-role"

*   Add a configuration field in `crates/biome_configuration/src/analyzer/linter/rules.rs`:
    *   Field: `use_aria_props_supported_by_role` of type `Option<RuleConfiguration<biome_js_analyze::options::UseAriaPropsSupportedByRole>>`
    *   Add "useAriaPropsSupportedByRole" to the GROUP_RULES array.

*   Create a type alias in `crates/biome_js_analyze/src/options.rs`:
    *   `pub type UseAriaPropsSupportedByRole = <lint::nursery::use_aria_props_supported_by_role::UseAriaPropsSupportedByRole as biome_analyze::Rule>::Options;`

*   Implement role resolution for `menuitem` elements in `crates/biome_aria/src/roles.rs`:
    *   Resolve roles based on the "type" attribute: 
        *   `type="checkbox"` → MenuItemCheckboxRole
        *   `type="radio"` → MenuItemRadioRole
        *   Default → MenuItemRole

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.