Implement a function to parse an HTML string into an Abstract Syntax Tree (AST) for webpack. This function should be the default export from `lib/html/buildHtmlAst.js` and must handle various HTML structures, including elements, text, comments, and doctype declarations, while respecting HTML5 parsing rules and namespaces.

*   Export `buildHtmlAst` as the default function from `lib/html/buildHtmlAst.js`.
    *   Accept a single parameter: `html` (string).
    *   Return a `DocumentNode` with type "document" and a `children` array.
*   Ensure the AST includes:
    *   `ElementNode` with:
        *   `type`: "element"
        *   `tagName`: lowercase string
        *   `children`: array of nodes
        *   `attributes`: array of `AttributeNode` objects
        *   `selfClosing`: boolean
        *   `namespace`: number (0 for HTML, 1 for MathML, 2 for SVG)
        *   `end`: number (character offset)
    *   `TextNode` with:
        *   `type`: "text"
        *   `data`: string
        *   Merge adjacent text segments at the same level.
    *   `CommentNode` with:
        *   `type`: "comment"
        *   `data`: string (exclude `<!-- -->` delimiters)
        *   Include full text for bogus comments starting with `<?`.
    *   `DoctypeNode` with:
        *   `type`: "doctype"
*   Handle void elements (e.g., `img`, `br`, `input`) with `selfClosing` set to true and no children.
*   Parse attributes into `AttributeNode` objects with:
    *   `name`: string
    *   `value`: string (empty if no value provided)
    *   Correctly handle unquoted attribute values.
*   Implement HTML5 optional tag-omission rules:
    *   Auto-close `<p>` when a block-level element opens.
    *   Auto-close `<li>` and `<td>` when another of the same type opens at the same level.
*   Manage namespaces:
    *   Elements inside `<svg>`: `namespace` set to 2 (NS_SVG).
    *   Elements inside `<math>`: `namespace` set to 1 (NS_MATHML).
    *   All other elements: `namespace` set to 0 (NS_HTML).
    *   Inside SVG, `<foreignobject>` and `<desc>` revert to HTML namespace (0).
*   Preserve raw-text content in elements like `<script>` and `<style>` as a single `TextNode`.
*   Ensure each element node's `end` property reflects its closing position, even if implicitly closed.
*   Expose namespace constants as properties on `buildHtmlAst`:
    *   `buildHtmlAst.NS_HTML = 0`
    *   `buildHtmlAst.NS_MATHML = 1`
    *   `buildHtmlAst.NS_SVG = 2`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.