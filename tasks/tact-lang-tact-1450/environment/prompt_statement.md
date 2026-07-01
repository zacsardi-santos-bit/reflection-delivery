I want to move the Tact compiler CLI validation from CI shell scripts to a proper automated test suite, and refactor the compiler's programmatic entry point to use a cleaner API.

Right now, the CLI tests are a bunch of shell script steps in the CI workflow — they check things like whether the version flag works, whether compilation errors suppress stack traces, whether different mode flags produce the right outputs, and whether invalid flag combinations are rejected. This all works in CI but can't be run locally with normal test tooling and doesn't give structured pass/fail results.

I'd like to create automated end-to-end tests that cover all these behaviors: version output format, single-file compilation with each mode flag, config-based compilation with decompilation support, the priority of CLI flags over config-file settings, mutual exclusivity of certain flags, error exit codes, expression evaluation, and the disassembler tool behavior.

At the same time, the programmatic interface for running compilations should be updated so it takes an in-memory configuration object and virtual file system instances instead of file paths and config file locations. Several internal tools (like the map-test generator and compilation failure test helper) should be updated to use this new API. A utility should also be added to compile all contracts found matching certain file patterns in a folder, replacing the old approach of maintaining a large config file listing every contract.

The test utilities for the automated CLI tests — the command runner and the contract code generator — should be implemented in a way that is consistent with the Tact compiler's output file naming conventions.
