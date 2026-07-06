I'm working on the Gleam compiler and noticed it doesn't properly validate a specific misuse of the record update syntax.

*   When record update syntax (spread syntax) is used on a constructor that has only positional (unlabelled) fields — no named fields — the type checker must produce a compilation error titled 'Invalid record constructor'.

*   The error diagnostic label for using record update syntax with an unlabelled constructor must be exactly 'This constructor has no labelled fields', pointing to the constructor name token in the source.

*   The error explanation text must be exactly 'Only constructors with at least one labelled field can be used with the update syntax.'

*   This validation must apply to record update expressions appearing in function bodies.

*   This validation must also apply to record update expressions appearing in constant definitions.

*   The existing snapshot file at 'compiler-core/src/type_/snapshots/gleam_core__type___tests__const_record_update_unlabelled_fields.snap' must be updated so that the constructor label reads 'This constructor has no labelled fields' and the explanation reads 'Only constructors with at least one labelled field can be used with the update syntax.' (replacing the old label 'This is not a record constructor' and old explanation 'Only record constructors can be used with the update syntax.').

*   The existing snapshot file at 'compiler-core/src/type_/snapshots/gleam_core__type___tests__const_record_update_variant_without_args.snap' must be updated with the same new label and explanation text as above.


*   Interface details: The implementation requires changes to the Gleam compiler's type error system. No new public functions or classes are called by name from the tests — tests exercise the compiler's behavior by compiling Gleam source and comparing error output to snapshot files.

The implementation must produce the exact error messages defined in the new snapshot files. Additionally, two existing snapshot files (for pre-existing but currently failing tests) must be updated to use the new error messages.

**Snapshot files to CREATE** (new, from test.patch):

- `compiler-core/src/type_/tests/snapshots/gleam_core__type___tests__errors__record_update_with_constructor_with_no_labelled_fields.snap`
- `compiler-core/src/type_/tests/snapshots/gleam_core__type___tests__errors__const_record_update_with_constructor_with_no_labelled_fields.snap`

**Snapshot files to UPDATE** (pre-existing, currently using old error messages):

- `compiler-core/src/type_/snapshots/gleam_core__type___tests__const_record_update_unlabelled_fields.snap`
  - Old label text: `This is not a record constructor`
  - New label text: `This constructor has no labelled fields`
  - Old explanation: `Only record constructors can be used with the update syntax.`
  - New explanation: `Only constructors with at least one labelled field can be used with the update syntax.`

- `compiler-core/src/type_/snapshots/gleam_core__type___tests__const_record_update_variant_without_args.snap`
  - Old label text: `This is not a record constructor`
  - New label text: `This constructor has no labelled fields`
  - Old explanation: `Only record constructors can be used with the update syntax.`
  - New explanation: `Only constructors with at least one labelled field can be used with the update syntax.`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.