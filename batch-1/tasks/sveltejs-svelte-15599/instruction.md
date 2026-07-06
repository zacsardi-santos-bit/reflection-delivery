Implement a more compact, declarative descriptor format for the Svelte compiler's client-side functional rendering mode. Replace the verbose, imperative JavaScript code with a nested array/object descriptor that directly describes the element tree structure.

*   Modify the Svelte compiler to emit a declarative array descriptor as the first argument to the template function call.
    *   Replace the imperative factory function with this descriptor.
*   Represent element nodes as objects with:
    *   An 'e' key containing the element tag name as a string.
    *   Optional 'c' key for child nodes, which is an array.
    *   Optional 'p' key for attributes, which is an object.
*   Ensure the 'c' key contains:
    *   An array of child descriptors.
    *   Plain strings for text nodes.
    *   Element objects for nested elements.
    *   Sparse array holes for comment/anchor nodes.
*   Ensure the 'p' key contains:
    *   An object with attribute/property names as keys and their static string values.
*   Maintain text nodes as plain string values directly in the descriptor array or in a 'c' children array.
*   Represent comment/anchor nodes as sparse array holes in the descriptor array or in a 'c' children array.
*   Keep the second argument (flags) to the template function call unchanged.
*   Use the same object descriptor format for SVG and MathML elements without explicit namespace qualifiers.
*   Update the runtime template function to:
    *   Accept the new array descriptor format as its first argument.
    *   Construct the equivalent DOM fragment from the descriptor.
    *   Apply static attributes from the 'p' object.
    *   Recursively build children from the 'c' array.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.