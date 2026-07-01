I'm working on the Ruff type checker and I'd like to add a native suppression comment format so that users can suppress type-checking errors using a comment style that belongs to this tool specifically, rather than always relying on the borrowed syntax from other type checkers.

The new comment format should let users suppress all errors on a line with a simple inline comment, or optionally name specific diagnostic codes to suppress only those. Multiple codes should be listable in a single comment. The parser should be forgiving about extra whitespace around the separator and within the code list, and should allow an optional trailing comma. An empty code list or invalid characters in code names should not suppress anything. Syntax errors and type-introspection diagnostics should remain unsuppressible.

While working on this, I also noticed that the existing suppression mechanism fails to recognize a suppression comment when it appears after another inline comment directive on a continuation line — for example, when a code formatting directive precedes the suppression on the same token. That should be fixed at the same time.

To make the suppression parser aware of known diagnostic codes, the database interface will need a new method that exposes the lint registry to the suppression logic.
