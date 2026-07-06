I'm working on integrating patch awareness into the dependency resolution phase of a package manager.

*   ResolveDependencyTreeOptions must include a new field `patched_dependencies: Option<Arc<PatchGroupRecord>>`. All existing call sites that construct this struct must be updated to supply the field (set to `None` when no patching is required).

*   ResolveImporterOptions must include a new field `patched_dependencies: Option<Arc<PatchGroupRecord>>`. This field is forwarded to the inner ResolveDependencyTreeOptions used by the orchestrator.

*   ResolvedTree must include an `applied_patches` field (a set of strings, e.g. HashSet<String>) that records which patch keys were successfully matched during resolution. It must support `contains` and `is_empty` operations.

*   ResolveDependencyTreeError must have a PatchKeyConflict variant. This variant is returned by resolve_dependency_tree when two or more range-based patch entries in patched_dependencies both satisfy the resolved version of the same package.

*   When patched_dependencies contains an exact-version entry matching a resolved package's name and version, resolve_dependency_tree must append `(patch_hash=<hash>)` to the package ID (e.g. `foo@1.0.0` becomes `foo@1.0.0(patch_hash=abc123)`) and add the patch key (e.g. `foo@1.0.0`) to ResolvedTree.applied_patches.

*   When patched_dependencies contains a range entry whose version range satisfies the resolved package's version (via semver satisfies), resolve_dependency_tree must append `(patch_hash=<hash>)` to the package ID and add the configured range key (e.g. `foo@^1.0.0`) to ResolvedTree.applied_patches.

*   When two or more range-based patch entries in patched_dependencies all satisfy the resolved version of a package, resolve_dependency_tree must return Err(ResolveDependencyTreeError::PatchKeyConflict(_)) rather than silently picking one patch.

*   When patched_dependencies is Some but no entry matches any resolved package (e.g. the patch is for a different package name or an unresolved version), all package IDs must remain unchanged and ResolvedTree.applied_patches must be empty.

*   After resolve_dependency_tree produces a ResolvedTree with patched package IDs, calling resolve_peers on that tree must propagate the full patched IDs (including the `(patch_hash=...)` suffix) to the DepPath values in direct_dependencies_by_alias.

*   The pacquet_patching crate must publicly export ExtendedPatchInfo, PatchGroup, PatchGroupRangeItem, and PatchGroupRecord so that callers can construct patched_dependencies values. PatchGroup must derive Default.


*   Interface details: ## Structs to Modify

Type: Struct field addition
Name: ResolveDependencyTreeOptions
Location: pacquet/crates/resolving-deps-resolver/src/resolve_dependency_tree.rs
Description: Configuration struct for the dependency-tree-walking function. Must gain a new field `patched_dependencies: Option<Arc<PatchGroupRecord>>`. When `None`, no patching is performed. When `Some`, the grouped patch records are consulted for each resolved package.
Signature: patched_dependencies: Option<Arc<PatchGroupRecord>>

Type: Struct field addition
Name: ResolveImporterOptions
Location: pacquet/crates/resolving-deps-resolver/src/resolve_importer.rs
Description: Configuration struct for the multi-pass peer-hoisting orchestrator. Must gain a new field `patched_dependencies: Option<Arc<PatchGroupRecord>>` that is forwarded to the inner `ResolveDependencyTreeOptions`.
Signature: patched_dependencies: Option<Arc<PatchGroupRecord>>

Type: Struct field addition
Name: ResolvedTree
Location: pacquet/crates/resolving-deps-resolver/src/resolved_tree.rs
Description: The output of dependency tree resolution. Must have an `applied_patches` field (a set of strings, e.g. `HashSet<String>`) that records which patch keys were matched during resolution. Supports `.contains(&str)` and `.is_empty()`.
Signature: applied_patches: HashSet<String>   (or equivalent set type)

## Error Variant

Type: Enum variant
Name: ResolveDependencyTreeError::PatchKeyConflict
Location: pacquet/crates/resolving-deps-resolver/src/resolve_dependency_tree.rs
Description: New variant added to the existing `ResolveDependencyTreeError` error enum. Returned by `resolve_dependency_tree` when two or more range-based patch entries in `patched_dependencies` both satisfy the resolved version of the same package, making it ambiguous which patch to apply.
Signature: PatchKeyConflict(/* conflict info */)

## Required patching types (must be publicly exported from pacquet_patching)

Type: Struct
Name: ExtendedPatchInfo
Location: pacquet/crates/patching/src/types.rs
Description: A patch entry tagged with its original configured key. Fields: `hash: String` (patch fingerprint), `patch_file_path: Option<PathBuf>`, `key: String` (the original patchedDependencies key, e.g. "foo@1.0.0" or "foo@^1.0.0").

Type: Struct
Name: PatchGroupRangeItem
Location: pacquet/crates/patching/src/types.rs
Description: One (version-range, patch) pair used in the range-based matching list. Fields: `version: String` (a semver range string), `patch: ExtendedPatchInfo`.

Type: Struct
Name: PatchGroup
Location: pacquet/crates/patching/src/types.rs
Description: All patches configured for a single package name, partitioned by match flavor. Fields: `exact: BTreeMap<String, ExtendedPatchInfo>` (keyed by exact version string) and `range: Vec<PatchGroupRangeItem>`. Must derive `Default`.

Type: Type alias
Name: PatchGroupRecord
Location: pacquet/crates/patching/src/types.rs
Description: The top-level patched-dependencies map, keyed by package name. Defined as `BTreeMap<String, PatchGroup>`. Publicly exported from `pacquet_patching`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.