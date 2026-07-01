Implement a new test fixture to ensure that inline comments in state properties of Mitosis components do not interfere with compilation. Ensure the component compiles correctly across all supported frameworks without including comment text in the output.

*   Create a new fixture file:
    *   File name: `store-comment.raw.tsx`
    *   Location: `packages/core/src/__tests__/data/store/`
    *   Content: Define a Mitosis component using the store hook with:
        *   A state property with an inline comment after its value.
        *   A state method without any inline comment.

*   Register the new fixture:
    *   Add an entry 'StoreComment' in the `BASIC_TESTS` object within `packages/core/src/__tests__/test-generator.ts`.
    *   Load the fixture using `getRawFile('./data/store/store-comment.raw.tsx')`.

*   Ensure correct parsing of the StoreComment fixture:
    *   The component's AST must have:
        *   Name: 'StringLiteralStore'
        *   State property 'foo' with:
            *   Type: 'property'
            *   Code: 'true' (without the inline comment)
            *   PropertyType: 'normal'
    *   State method 'bar' must have:
        *   Type: 'method'
        *   Code: 'bar() {}' (without any comment text)

*   Verify correct compilation:
    *   When compiling the StoreComment component to any supported target framework, ensure:
        *   The generated output does not include inline comment text.
        *   The structure matches expected output for each target, with:
            *   State property 'foo' having value 'true'
            *   Method 'bar' defined correctly

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.