Our project is currently broken — it won't compile because a cryptography library dependency is pinned to an older version that has become incompatible with the rest of the dependency graph. This is blocking all integration tests from running, including the ones that verify our DNS challenge solver works correctly.

I need to update the cryptography library and its related transitive dependencies (the system-level library, terminal library, and text processing library) to newer compatible versions. These updates need to be applied consistently across the entire repository — the root module files, all submodule files, their corresponding checksum files, and the license attribution files in each subdirectory.

The target versions are the same compatible versions already reflected in the test subdirectory module files. Once the root module and all other module files are updated to match those versions, the project should compile again and the DNS challenge solver tests should pass.
