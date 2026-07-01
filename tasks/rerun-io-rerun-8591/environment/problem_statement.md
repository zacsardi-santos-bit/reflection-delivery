## Description

The component bundle interface in the component data model library currently has a blanket implementation that automatically makes every individual component type act as a full multi-component bundle. This is both confusing and dangerous: code that expects a bundle containing several distinct named batches can silently receive a single component without any explicit intent from the developer, masking potential bugs and making APIs harder to reason about.

The trait definition and all its implementations also live inline in the main library entry point file, making it harder to organize, read, and extend.

## Expected Behavior

- The bundle interface should be moved into its own dedicated module and source file, and re-exported from the crate root as before so callers are unaffected.
- The blanket implementation that gave every individual component automatic bundle status must be removed. A single component should no longer implicitly satisfy the bundle interface.
- Developers who need to use a single component as a bundle should do so explicitly — by first treating it as a batch reference and then wrapping it in a slice of batch references.
- Arrays and slices of explicit batch references should naturally implement the bundle interface, each element contributing one batch to the collection.
- All existing collection variants (slices, vecs, fixed-size arrays) for both raw batch references and bundle-implementing types must continue to be supported.

## Why This Matters

Removing the implicit coercion makes intent explicit and prevents accidental type confusion. APIs that accept a bundle of components now correctly reject a bare component, requiring the developer to be deliberate about what they are passing. This is a correctness and safety improvement for anyone building on top of the component system.
