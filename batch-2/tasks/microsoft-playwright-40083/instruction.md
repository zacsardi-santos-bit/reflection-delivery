I'm working on a page that shows multiple alert notifications, each with the same accessible name like "Upload successful" but with different descriptions explaining which file was affected.

*   The role-based locator (getByRole) must accept a new 'description' option of type string or RegExp that filters matched elements by their accessible description.

*   By default, description matching is case-insensitive and searches for a substring within the element's accessible description. When 'exact' is true (and the description is a string), matching must be case-sensitive and require a full-string match (with whitespace trimmed).

*   When the 'description' option is a RegExp, the regex is used directly to match the accessible description without applying the exact flag.

*   The 'exact' option applies to both 'name' and 'description' simultaneously when either is a string value. It must not emit an 'exact' setting in code output when only a regex description is used.

*   The accessible description is resolved from: the element's aria-description attribute, the text content of elements referenced by aria-describedby, and the element's title attribute as a fallback.

*   Both the element's accessible description and the provided description search string must undergo whitespace normalization before comparison.

*   The internal role selector format must support a 'description' attribute — for example, 'internal:role=button[description="Upload report"s]' for case-sensitive exact match and 'internal:role=alert[description="value"i]' for case-insensitive match.

*   The error message thrown when an unknown attribute is used in a role selector must now include 'description' in the sorted list of valid attribute names. The exact list must be: "checked", "description", "disabled", "expanded", "include-hidden", "level", "name", "pressed", "selected".

*   The automatic selector generator must include the accessible description in a generated selector when the element's accessible name alone is not sufficient to uniquely identify the element among siblings with the same role.

*   The automatic selector generator must not include the accessible description when the element's accessible name is already unique enough to identify it.

*   When both the accessible name and accessible description are shared by multiple elements with the same role, the selector generator must fall back to an nth-based selector (e.g., '>> nth=0') rather than using description.

*   The description option must work for elements that have no accessible name — the description alone can be used to uniquely identify such elements in generated selectors.

*   Locator code generation must output the description option correctly for each target language: JavaScript as 'description: value', Python as 'description=value', Java as '.setDescription(value)', C# as 'Description = value' (string) or 'DescriptionRegex = new Regex("...")' (regex).


*   Interface details: Type: TypeScript Interface Extension
Name: ByRoleOptions
Location: packages/isomorphic/locatorUtils.ts
Description: The existing ByRoleOptions type must be extended with a new optional field: `description?: string | RegExp`. This is used by getByRoleSelector() to generate the internal role selector string.

Type: Function
Name: getByRoleSelector
Location: packages/isomorphic/locatorUtils.ts
Signature: getByRoleSelector(role: string, options?: ByRoleOptions): string
Description: Builds an internal role selector string. When options.description is provided, it must emit a `[description=...]` clause in the selector string, using escapeForAttributeSelector with the exact flag. The description field must be accepted in ByRoleOptions alongside name, checked, disabled, expanded, exact, includeHidden, level, pressed, selected.

Type: Function
Name: getElementAccessibleDescription
Location: packages/injected/src/roleUtils.ts (exported from the roleUtils module)
Signature: getElementAccessibleDescription(element: Element, includeHidden: boolean): string
Description: Returns the accessible description of the given element, resolved from aria-description, aria-describedby referenced element text, or title attribute as a fallback. Must be exported from roleUtils so it can be used in both the role selector engine and the selector generator.

Type: Internal Selector Engine Extension
Name: internal:role description attribute
Location: packages/injected/src/roleSelectorEngine.ts
Description: The role selector engine must recognize 'description' as a valid attribute name in internal:role selectors (e.g., `internal:role=alert[description="value"s]`). The attribute supports string and RegExp values. The sorted list of valid attribute names for error messages must be: "checked", "description", "disabled", "expanded", "include-hidden", "level", "name", "pressed", "selected".

Type: Locator Generator Extension (JavaScript)
Name: JavaScriptLocatorFactory — role case
Location: packages/isomorphic/locatorGenerators.ts
Description: When generating a getByRole locator in JavaScript with a description option: string description emits `description: 'value'`, regex description emits `description: /pattern/`. The `exact: true` option is emitted after description when either name or description is a string (not when only regex values are used).

Type: Locator Generator Extension (Python)
Name: PythonLocatorFactory — role case
Location: packages/isomorphic/locatorGenerators.ts
Description: When generating a getByRole locator in Python with a description option: string description emits `description="value"`, regex description emits `description=re.compile(r"pattern")`. The `exact=True` option is emitted when either name or description is a string.

Type: Locator Generator Extension (Java)
Name: JavaLocatorFactory — role case
Location: packages/isomorphic/locatorGenerators.ts
Description: When generating a getByRole locator in Java with a description option: string description emits `.setDescription("value")`, regex description emits `.setDescription(Pattern.compile("pattern"))`. The `.setExact(true)` option is emitted when either name or description is a string.

Type: Locator Generator Extension (C#)
Name: CSharpLocatorFactory — role case
Location: packages/isomorphic/locatorGenerators.ts
Description: When generating a getByRole locator in C# with a description option: string description emits `Description = "value"`, regex description emits `DescriptionRegex = new Regex("pattern")`. The `Exact = true` option is emitted when either name or description is a string.

Type: Selector Generator Extension
Name: selectorGenerator description usage
Location: packages/injected/src/selectorGenerator.ts
Description: The selector generator must use getElementAccessibleDescription() to retrieve the accessible description of each candidate element. When building role-based selector candidates, if an accessible description is available, additional candidates must be added that include a `[description=...]` clause using escapeForAttributeSelector with caseSensitive=true. Description-based candidates must have a slightly higher score than plain name-based candidates (to prefer them when uniqueness requires it). When a role has no accessible name, description-based candidates must still be generated.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.