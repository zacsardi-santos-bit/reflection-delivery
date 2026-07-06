# TypeScript Plugin for Payload Component Path Validation and IDE Assistance

## Description

Payload CMS configurations reference custom UI components using special path strings — a file path optionally followed by an export name. Developers writing these references have no IDE assistance: the editor does not validate whether the referenced file exists, whether the named export is correct, or offer any autocompletion while typing. Misspelled paths and non-existent export names go undetected until runtime.

## Expected Behavior

A new TypeScript language service plugin for Payload should provide:

- **Validation diagnostics**: Flag component path strings that reference a file that cannot be found (one error code for missing files, another for missing or wrong export names). When an export name is wrong but the file exists, the error message should suggest available exports from that module.
- **Autocompletion**: When typing a component path string, offer completions for file paths and subdirectories. After the export separator character, offer completions for the named exports of the resolved module. The object-based form of component references (with separate path and export name fields) should also receive appropriate completions for each field.
- **Go-to-definition**: Navigating to a component path string should jump to the referenced component's definition in the source file — including when the path uses directory-level references that resolve through an index file.
- **Default export awareness**: If a path has no explicit export selector and the target module has no default export, an error should be reported. If a default export is present, the path is valid without an explicit export selector.
- **Path alias support**: All of the above should work when component paths use aliases configured in the project's TypeScript settings (e.g., an alias that maps a short prefix to a source directory).

## Why This Matters

Without this plugin, Payload CMS developers have no immediate feedback when they mistype a component path or export name, leading to silent misconfiguration that only surfaces at runtime. This plugin brings the same IDE experience (errors, completions, jump-to-definition) to Payload component references that developers already expect for normal TypeScript imports.
