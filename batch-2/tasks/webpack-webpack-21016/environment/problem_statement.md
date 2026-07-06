## Description

Webpack needs a way to parse HTML strings into a structured tree (AST) so that plugins and core internals can traverse, inspect, and transform HTML content at the structural level — for example, to discover and process resource references in HTML entry points.

Currently there is no built-in HTML parser in the project. Adding one would let webpack understand the full structure of an HTML document: its elements, text content, attributes, comments, doctype declarations, and embedded namespace contexts like SVG and MathML.

## Expected Behavior

The parser should produce a tree where every node has a discriminating type field:

- The root is always a document node.
- HTML tags become element nodes, with a tag name, a list of children, and a list of parsed attributes (each with a name and value).
- Void elements (like image tags, line-break tags, and input tags) are flagged as self-closing and carry no children.
- Text content becomes text nodes with the raw string.
- HTML comments become comment nodes. Unusual comment-like constructs that do not use the standard comment syntax should also be treated as comments.
- DOCTYPE declarations become their own node type.
- The parser should handle optional tag-omission rules (e.g., paragraph elements closing before block elements, list items closing before the next list item).
- Elements inside SVG or MathML sections should be tagged with the appropriate namespace, and HTML integration points inside SVG (such as foreign-object content) should revert back to HTML namespace.
- Raw-text elements like script blocks should have their inner content preserved verbatim as a single text node, without any attempt to parse it as markup.
- Each element node should track its end position in the source string, including the correct end for implicitly-closed elements.
- Three numeric namespace constants (for HTML, MathML, and SVG) should be exported alongside the parser function so callers can compare against them.

## Why This Matters

Without a proper HTML AST, webpack cannot meaningfully analyze or rewrite HTML files during a build. This foundational parser is a prerequisite for higher-level HTML processing features.
