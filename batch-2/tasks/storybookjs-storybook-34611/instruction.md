Implement a utility to parse MDX documentation files and extract structured metadata from them. Ensure the utility validates metadata attributes and provides descriptive errors for incorrect formats. Handle cases where metadata is missing or malformed gracefully.

*   Implement `extractImports` function:
    *   Accept a Babel ESTree-compatible `Program` AST node.
    *   Return a plain object mapping each imported identifier or namespace alias to its source module path.
    *   Handle both named imports and namespace (star) imports.
    *   Process all import declarations, even if they span multiple statements.

*   Implement `analyzeMdx` function:
    *   Accept a raw MDX string.
    *   Return a Promise resolving to an object with fields: `imports`, `title`, `name`, `of`, `summary`, `isTemplate`, and `metaTags`.
    *   Extract and validate metadata from the `Meta` JSX element.

*   Metadata extraction and validation:
    *   `imports`: Return a flat array of all import source paths found in the MDX.
    *   `title`, `name`, `summary`: Extract as string literals. Reject with an error if not a string literal.
        *   Error messages: 
            *   "Expected string literal title, received JSXExpressionContainer"
            *   "Expected string literal name, received JSXExpressionContainer"
            *   "Expected string literal summary, received JSXExpressionContainer"
    *   `of`: Resolve to the import source path of the referenced identifier. Reject if unknown or a string literal.
        *   Error messages:
            *   "Unknown identifier <identifierName>"
            *   "Expected JSX expression, received Literal"
    *   `isTemplate`: Default to false. Accept implicit boolean or boolean expression. Reject non-boolean or string literals.
        *   Error messages:
            *   "Expected expression isTemplate, received Literal"
            *   "Expected boolean isTemplate, received <typeof value>"
    *   `metaTags`: Extract as an array of string values. Reject if not a JSX expression or if elements are not string literals.
        *   Error messages:
            *   "Expected JSX expression tags, received Literal"
            *   "Expected string literal tag, received Literal"

*   Handle multiple or missing `Meta` elements:
    *   Reject if more than one `Meta` JSX element is present with "Meta can only be declared once".
    *   Resolve with default values if no `Meta` JSX element is present.

*   Handle malformed MDX:
    *   Resolve without throwing, using available import information.
    *   Keep Meta-specific fields at defaults if parsing fails.

*   Ensure the function does not fail on documents with exported declarations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.