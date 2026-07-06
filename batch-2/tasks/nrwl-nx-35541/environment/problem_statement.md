## Description

The devkit package is being updated to include a strict exports map, which means deep import paths (those pointing directly into internal source directories) are no longer valid. Any existing workspace code that imports from these deep paths will break at runtime or build time once the exports restriction is in place.

We need an automated migration that scans TypeScript source files across the workspace and rewrites all such deep imports to the correct, officially supported entry points. Symbols that are part of the stable public API should be redirected to the main package export, while symbols that are only available through the internal subpath should be redirected there.

## Expected Behavior

- The migration should process TypeScript source files (including the common TypeScript module format variants such as .ts, .tsx, .cts, and .mts) only; non-TypeScript files such as documentation should remain untouched.
- Named imports should be split intelligently: public symbols go to the main package, internal symbols go to the internal subpath.
- When a single import statement contains a mix of public and internal symbols, it should be split into two separate declarations.
- Import aliases, type-only imports, inline type modifiers, and multi-line import formatting should all be handled gracefully.
- For import forms that cannot be split by symbol (default imports, namespace imports, side-effect imports, synchronous module loading calls, and dynamic imports), the specifier should fall back to the internal subpath as the safest default.
- After rewriting, the migration should collapse any duplicate import declarations so a file never ends up with two import lines pointing to the same entry point. This includes merging into imports already present in the file before the migration ran.

## Why This Matters

Without this migration, upgrading the devkit package will silently break every project that relies on deep import paths. Automating the rewrite ensures developers can safely upgrade without having to manually audit and update every affected file.
