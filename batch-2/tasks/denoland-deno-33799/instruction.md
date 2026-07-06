I'm working with a monorepo where we use a workspace catalog to pin shared dependency versions.

*   The NpmOverrides::from_value function must accept a third parameter of type &Catalogs in addition to its existing value and root_deps parameters. All callers of this function must be updated to pass the catalogs argument.

*   A public type alias named Catalogs must be defined in libs/npm/resolution/overrides.rs as IndexMap<String, IndexMap<String, String>>, representing a mapping of catalog name to a mapping of package name to version string.

*   When an override value string is "catalog:" (with an empty suffix), the system must look up the package in the catalog named "default" and resolve the override to that version.

*   When an override value string is "catalog:<name>" with a non-empty name (e.g., "catalog:react18"), the system must look up the package in the catalog with that exact name and resolve the override to the version found there.

*   When an override key contains a version selector (e.g., "foo@^1.0.0"), the catalog lookup must use only the package name portion ("foo"), not the full key string including the selector.

*   A "catalog:" override value must also be supported when it appears as the dot key ("." key) within a nested override object.

*   When a "catalog:" or "catalog:<name>" reference is used but the package name is not found in the specified catalog, NpmOverrides::from_value must return Err(NpmOverridesError::UnresolvedCatalog { key, catalog }) where key is the override key string and catalog is the resolved catalog name ("default" for an empty suffix).

*   The NpmOverridesError::UnresolvedCatalog variant must produce an error message matching the format: "Override \"catalog:{catalog}\" for key \"{key}\" could not be resolved (package not found in catalog)". The error message must contain both the word "catalog" and the override key name.

*   The version string retrieved from the catalog must be parsed as an npm version requirement (VersionReq::parse_from_npm). If parsing fails, an NpmOverridesError::ValueParse error must be returned.


*   Interface details: Type: Type Alias
Name: Catalogs
Location: libs/npm/resolution/overrides.rs
Signature: pub type Catalogs = IndexMap<String, IndexMap<String, String>>;
Description: Represents workspace catalogs. Maps catalog name (e.g., "default", "react18") to a map of package name to version string. Requires `indexmap::IndexMap`.

Type: Function
Name: NpmOverrides::from_value
Location: libs/npm/resolution/overrides.rs
Signature: pub fn from_value(value: serde_json::Value, root_deps: &HashMap<PackageName, StackString>, catalogs: &Catalogs) -> Result<Self, NpmOverridesError>
Description: Parses an NpmOverrides instance from a JSON value. The new third parameter catalogs provides the workspace catalog data used to resolve "catalog:" and "catalog:<name>" override values. All existing callers (including in graph.rs and factory.rs) must be updated to pass this argument.

Type: Enum Variant
Name: NpmOverridesError::UnresolvedCatalog
Location: libs/npm/resolution/overrides.rs
Signature: UnresolvedCatalog { key: String, catalog: String }
Description: Error returned when a catalog: reference cannot be resolved because the package name is not present in the specified catalog. The error message must match the format: "Override \"catalog:{catalog}\" for key \"{key}\" could not be resolved (package not found in catalog)". The error message must contain both the string "catalog" and the override key name.

Type: Function (private)
Name: resolve_catalog_override
Location: libs/npm/resolution/overrides.rs
Signature: fn resolve_catalog_override(key: &str, catalog_name: &str, catalogs: &Catalogs) -> Result<NpmOverrideValue, NpmOverridesError>
Description: Resolves a catalog: reference by extracting the package name from the override key (stripping any version selector), looking it up in the named catalog (treating an empty catalog_name as "default"), and returning NpmOverrideValue::Version with the parsed version requirement. Returns NpmOverridesError::UnresolvedCatalog if the package is not found.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.