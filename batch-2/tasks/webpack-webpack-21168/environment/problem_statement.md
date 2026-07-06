## Description

The HTML and CSS parsing utilities in webpack's internal library are currently split across multiple module files. To work with HTML parsing, a developer must import from one module for the tree-building function and namespace constants, and from a separate module for the tokenizer function, quote-type constants, and entity-decoding utilities. CSS parsing utilities are similarly scattered. This fragmentation forces consumers to track multiple module paths just to access the tools for a single parsing domain.

## Expected Behavior

- A single entry-point module for all HTML parsing exports (AST builder, tokenizer, namespace constants, quote-type constants, and entity decoding functions) should be available so consumers can import everything HTML-related from one place.
- A single entry-point module for all CSS parsing exports (tokenizer utilities, identifier helpers, and CSS constant values) should be available so consumers can import everything CSS-related from one place.
- Both new unified modules should expose all the same functions, constants, and TypeScript type definitions that previously existed in the individual specialized modules.
- The HTML unified module must expose its tree-building function as a named export (not a default export), so that parts of it can be individually replaced in tests without affecting the rest of the module.

## Why This Matters

Having fragmented module paths for a single parsing domain makes the internal API harder to discover and maintain. Consolidating each domain into a single "syntax" entry point reduces the number of import paths developers must remember and makes the public surface of each domain self-contained.
