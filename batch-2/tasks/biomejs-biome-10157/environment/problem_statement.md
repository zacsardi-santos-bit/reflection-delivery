## Description

The HTML parser currently produces confusing and incorrect output when it encounters a closing tag for a void element (elements that cannot have children, like line break elements or input fields). There are two distinct problems:

1. **Multiple conflicting diagnostics**: When a void element is followed by a closing tag, the parser emits two separate parse errors — one about the void element not being allowed to have a closing tag, and another about a mismatched or unexpected closing tag. These two errors are redundant and contradictory, making it unclear what the user should do to fix their HTML.

2. **Incorrect document hierarchy**: More critically, the parser mistakenly treats the spurious void closing tag as a structural close of the nearest enclosing parent element. This means the document tree is parsed incorrectly — the parent element appears to close early, even though the real closing tag for that parent appears later in the document. This is a correctness bug that can affect downstream processing of the parsed HTML.

## Expected Behavior

- When a void element is followed by a closing tag, exactly one diagnostic should be emitted that clearly tells the user to remove the closing tag.
- The spurious closing tag must be recognized as invalid and treated as a bogus/error node in the document tree — it should not close any enclosing element.
- The enclosing parent element's structure should remain intact, closed only by its own proper matching closing tag.

## Why This Matters

Users seeing multiple conflicting parse errors for a simple mistake are confused about what action to take. Additionally, the hierarchy corruption means downstream formatters, linters, or other tools operating on the parsed tree may produce wrong results for otherwise valid surrounding HTML.
