## Description

We should add a new lint rule to detect and warn about "barrel files" — files that consist entirely or primarily of re-export statements, forwarding symbols from other modules to a single convenient entry point.

Barrel files are a widespread pattern in JavaScript and TypeScript projects, but they carry a real cost: bundlers and analysis tools are forced to load entire module graphs even when only a small subset of the re-exported symbols are actually used. This leads to slower build times, increased memory usage, and unnecessarily large bundles.

## Expected Behavior

- Any re-export statement that forwards values from another module (wildcard re-exports, named re-exports, aliased re-exports, or default-as-named re-exports) should produce a lint warning.
- The warning message should explain that barrel files slow down performance and cause large module graphs with unused modules, and should direct the developer to a more thorough explanation.
- Type-only re-exports should be exempt from this rule, because they carry no runtime cost and do not contribute to enlarged module graphs at build time.

## Why This Matters

Projects at scale commonly reach a point where barrel files silently become a performance bottleneck for tooling. Having an automatic lint rule lets teams proactively prevent this anti-pattern from being introduced, or identify existing barrel files so they can be restructured. Type-only re-exports are a safe exception because they are erased at compile time and do not affect runtime module loading.
