## Description

The Noir language server should provide code actions to help developers resolve unrecognized identifiers. When a developer writes code that references a type or module that exists in the project but hasn't been imported or qualified, the editor should offer quick-fix options to either add an import statement or use the fully qualified path.

## Expected Behavior

- When the cursor is on an unrecognized type or module name, the language server should return code actions offering to:
  - **Import the symbol**: Automatically insert the appropriate import statement at the top of the file, making the bare identifier valid without further changes.
  - **Qualify the reference**: Replace the bare identifier in-place with its fully qualified path (i.e., all containing module names joined together in order).
- This should work for both struct types and modules that are defined in nested submodule hierarchies.
- The language server must have a code action request handler implemented at the standard location for request handlers.

## Why This Matters

Noir projects can have types and modules organized across multiple nested modules. Currently, developers have no editor assistance to resolve an unrecognized identifier — they must manually look up the full path and either write an import or type out the qualified name. Adding import and qualify code actions makes the development experience significantly smoother and brings it in line with what modern language servers offer for other languages.
