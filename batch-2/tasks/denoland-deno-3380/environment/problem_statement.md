# Add CommonJS module loading support to Deno's Node compatibility layer

## Description

Deno's standard library does not currently support loading CommonJS (CJS) modules — the module format used by the vast majority of existing Node.js packages. Developers porting Node.js applications to Deno, or trying to reuse Node.js packages within Deno, have no way to consume these modules.

We need a way to load CJS modules from within Deno, including support for:

- **Relative requires** — loading other local `.js` files relative to the requiring module
- **Subdirectory resolution** — resolving modules in subdirectories (e.g. `./subdir/module`)
- **Node modules resolution** — loading third-party packages from a `node_modules` directory
- **Circular dependency handling** — gracefully handling modules that create circular dependency chains without crashing

## Expected Behavior

- A factory function creates a `require`-like loader anchored to a given file's location
- The loader resolves and evaluates a CJS module, returning its `module.exports` value
- Transitive dependencies (including node_modules packages) are resolved correctly
- Circular dependencies do not crash the runtime or cause infinite loops

## Why This Matters

Without this, Deno cannot interoperate with the enormous ecosystem of CommonJS Node.js packages. This is a foundational step toward making Deno a drop-in (or near drop-in) replacement for Node.js environments.
