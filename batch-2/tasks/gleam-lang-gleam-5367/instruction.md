I've been working on a Gleam project with multiple dependencies and ran into a frustrating issue: when two different dependency packages both define a module with the same name, the compiler doesn't detect and clearly report this conflict.

*   The `DefinedModuleOrigin` struct must be defined in `compiler-core/src/error.rs` and must have two public fields: `package_name: EcoString` (the name of the package defining the module) and `path: Utf8PathBuf` (the path to the module source file). It must derive `Debug`, `Clone`, `Eq`, and `PartialEq`.

*   The `Error::DuplicateModule` enum variant's `first` and `second` fields must be changed from `Utf8PathBuf` to `DefinedModuleOrigin`. This change must propagate to all call sites throughout the compiler.

*   The `already_defined_modules` map parameter in `PackageCompiler::compile()` must change its value type from `Utf8PathBuf` to `DefinedModuleOrigin` to carry package name and path information alongside module names.

*   When two modules with the same name are defined by two DIFFERENT packages, the duplicate module error's formatted message must read: 'It is first defined by the package <first_package_name>' followed by 'It is defined a second time by the package <second_package_name>'. The full error output must match exactly: 'error: Duplicate module\n\nThe module `<name>` is defined multiple times.\n\nIt is first defined by the package <first>\nIt is defined a second time by the package <second>'.

*   When two modules with the same name are defined within the SAME package (same package_name in both DefinedModuleOrigin values), the duplicate module error's formatted message must use file paths instead of package names. It must read: 'It is first defined at <first_path>' followed by 'It is defined a second time at <second_path>'.

*   The insta snapshot file for the cross-package duplicate module error must be created at `compiler-core/src/build/package_compiler/snapshots/gleam_core__build__package_compiler__tests__different_packages_defining_duplicate_module.snap` with the exact content specified in the interface.

*   The insta snapshot file for the same-package duplicate module error must be created at `compiler-core/src/build/package_compiler/snapshots/gleam_core__build__package_compiler__tests__same_package_defining_duplicate_module.snap` with the exact content specified in the interface.

*   The existing snapshot files for `duplicate_module`, `duplicate_module_dev`, and `duplicate_module_test_dev` tests in `test-package-compiler/src/snapshots/` must be updated to use the new error message format ('It is first defined at ...' / 'It is defined a second time at ...') instead of the old format ('First: ...' / 'Second: ...').


*   Interface details: Type: Struct
Name: DefinedModuleOrigin
Location: compiler-core/src/error.rs
Description: Describes where a defined module comes from — its package name and file path. Derives Debug, Clone, Eq, PartialEq. Used as the value type in the `already_defined_modules` map passed to the compiler.
Fields:
  - package_name: EcoString  (the name of the package that defines the module)
  - path: Utf8PathBuf        (the file path of the module source file)

Type: Enum Variant
Name: Error::DuplicateModule
Location: compiler-core/src/error.rs
Description: Error raised when the same module name is defined more than once. The `first` and `second` fields must both be `DefinedModuleOrigin` (previously they were `Utf8PathBuf`).
Signature:
  DuplicateModule {
      module: Name,
      first: DefinedModuleOrigin,
      second: DefinedModuleOrigin,
  }

Type: Method (signature change)
Name: PackageCompiler::compile
Location: compiler-core/src/build/package_compiler.rs
Description: The `already_defined_modules` parameter type must change from `&mut im::HashMap<EcoString, Utf8PathBuf>` to `&mut im::HashMap<EcoString, DefinedModuleOrigin>`.
Signature: compile(self, warnings: &WarningEmitter, existing_modules: &mut im::HashMap<EcoString, type_::ModuleInterface>, already_defined_modules: &mut im::HashMap<EcoString, DefinedModuleOrigin>, stale_modules: &mut StaleTracker, incomplete_modules: &mut HashSet<EcoString>, telemetry: &dyn Telemetry) -> Outcome<Compiled, Error>

Type: Snapshot File
Name: gleam_core__build__package_compiler__tests__different_packages_defining_duplicate_module.snap
Location: compiler-core/src/build/package_compiler/snapshots/gleam_core__build__package_compiler__tests__different_packages_defining_duplicate_module.snap
Description: Insta snapshot for the error output when two different packages define the same module. Exact file content must be:
---
source: compiler-core/src/build/package_compiler/tests.rs
expression: output
---
error: Duplicate module

The module `a_module` is defined multiple times.

It is first defined by the package dep1
It is defined a second time by the package a_package

Type: Snapshot File
Name: gleam_core__build__package_compiler__tests__same_package_defining_duplicate_module.snap
Location: compiler-core/src/build/package_compiler/snapshots/gleam_core__build__package_compiler__tests__same_package_defining_duplicate_module.snap
Description: Insta snapshot for the error output when the same package defines the same module twice. Exact file content must be:
---
source: compiler-core/src/build/package_compiler/tests.rs
expression: output
---
error: Duplicate module

The module `a_module` is defined multiple times.

It is first defined at a_package/a_module.gleam
It is defined a second time at /src/a_module.gleam


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.