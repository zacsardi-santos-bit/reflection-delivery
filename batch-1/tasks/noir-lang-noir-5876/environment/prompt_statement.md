I'm working on the Noir language server and I'd like to add code actions that help developers resolve unrecognized identifiers. Right now, when someone writes code that references a type or module that exists somewhere in the project but isn't in scope, the editor shows an error but offers no way to fix it automatically.

I want the language server to provide two kinds of code actions in this situation: one that adds an import statement at the top of the file so the bare identifier becomes valid, and another that replaces the identifier in-place with its fully qualified path. Both should work for struct types and for modules that are nested inside other modules.

The code action request handler needs to be implemented in the standard location for LSP request handlers in the project, and should integrate with the existing language server state.
