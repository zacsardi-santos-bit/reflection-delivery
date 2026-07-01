## Description

GoReleaser currently supports building and releasing projects written in Go, Rust, Zig, and Bun. However, there is no built-in support for the Deno runtime, which allows developers to compile TypeScript applications into self-contained native binaries. Deno users who want to use GoReleaser for their release workflow have no native integration — they must resort to workarounds using the prebuilt import mechanism.

## Expected Behavior

- A new builder option should be available for Deno projects, enabling GoReleaser to compile TypeScript source files into standalone native binaries across standard target platforms.
- The integration should support the same cross-platform targets that Deno's compiler supports, including Windows, macOS (Intel and Apple Silicon), and Linux (x86 and ARM).
- Target strings in Deno's native format should be parsed and translated to the conventional OS/architecture naming used throughout GoReleaser, so that templates and filters work consistently.
- The artifact filtering system should handle Deno-built artifacts the same way it handles artifacts from other non-Go builders (Bun, Rust, Zig) — particularly for architecture variant filters.
- A health-check should verify that the required Deno binary is present when a project is configured to use the Deno builder.
- An example configuration file should be provided to help users get started with Deno builds.

## Why This Matters

Deno is a growing TypeScript runtime with a strong cross-compilation story. Adding native GoReleaser support allows Deno developers to take full advantage of GoReleaser's release pipeline — including archives, changelogs, publishing, and more — without manual workarounds.
