Implement a new builder in GoReleaser to support Deno projects, allowing TypeScript applications to be compiled into native binaries across multiple platforms. Ensure the builder integrates seamlessly with existing GoReleaser workflows and supports artifact filtering and health-check systems.

*   Create a new package at `internal/builders/deno`:
    *   Self-register the builder under the name 'deno' using an init function.
    *   Implement the `Default` variable as a package-level default builder instance of type `*Builder`.

*   Implement the `Dependencies` method in `internal/builders/deno/build.go`:
    *   Return a non-empty slice containing the string 'deno'.

*   Implement the `Parse` method in `internal/builders/deno/targets.go`:
    *   Parse Deno target strings into a `Target` struct with fields: `Target`, `Arch`, `Vendor`, `Os`, and `Abi`.
    *   Return an error for strings with fewer than 3 dash-separated parts.

*   Implement the `WithDefaults` method in `internal/builders/deno/build.go`:
    *   Set default values for an empty `config.Build`: `Tool="deno"`, `Command="compile"`, `Dir="."`, `Main="main.ts"`, and `Targets=defaultTargets()`.
    *   Return an error if any target in `Targets` is not valid per `isValid()`.

*   Implement the `defaultTargets` function in `internal/builders/deno/targets.go`:
    *   Return the list: `["x86_64-pc-windows-msvc", "x86_64-apple-darwin", "aarch64-apple-darwin", "x86_64-unknown-linux-gnu", "aarch64-unknown-linux-gnu"]`.

*   Implement the `isValid` function in `internal/builders/deno/targets.go`:
    *   Return true for each of the five default targets and false for unrecognized strings.

*   Implement the `Build` method in `internal/builders/deno/build.go`:
    *   Produce a `Binary` artifact with `Extra` map entries: `ExtraBuilder='deno'`, `ExtraBinary`, `ExtraExt`, `ExtraID`.
    *   Translate Deno arch names to Go-style names: `aarch64` to `arm64`, `x86_64` to `amd64`.

*   Update artifact filtering:
    *   Ensure `ByGoamd64` and `ByGoarm` treat artifacts with `ExtraBuilder='deno'` consistently with other non-Go builders.

*   Provide example configurations:
    *   Embed `DenoExampleConfig` in `internal/static/config.go` from `config.deno.yaml`, ensuring it parses as a valid GoReleaser config with `Version=2` and `Builds[0].Builder='deno'`.
    *   Embed `RustExampleConfig` similarly for Rust.

*   Update the health-check system:
    *   Recognize 'deno' as a required system dependency when configured in a project.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.