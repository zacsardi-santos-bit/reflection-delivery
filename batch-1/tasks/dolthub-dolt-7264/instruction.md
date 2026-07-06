Implement a version-aware configuration system for Dolt's SQL server YAML configuration. Ensure each configuration field is annotated with the minimum server version it was introduced in, and create a mechanism to safely serialize configuration for older versions by removing unsupported fields. Enforce that fields with version annotations are nullable and optional in YAML output. Develop a validation system to ensure all fields have proper version annotations.

Requirements:

* Implement `nullUnsupported(verNum uint32, st any) error` in `go/cmd/dolt/commands/sqlserver/minver.go`:
    * Use reflection to iterate over struct fields.
    * Set fields tagged with `minver` as 'TBD' to their zero value (nil).
    * For fields with a version string, encode the version and set the field to nil if `verNum` is less than the encoded version.
    * Return an error if a field with a `minver` tag is not a nullable type or lacks 'omitempty' in its `yaml` tag.
    * Recursively apply the logic to nested structs, including pointer-to-struct fields and slices of structs.

* Implement `MinVerFieldInfo` struct in `go/cmd/dolt/commands/sqlserver/minver.go`:
    * Fields: `Name`, `TypeStr`, `MinVer`, `YamlTag`.

* Implement `MinVerFieldInfoFromLine(l string) (MinVerFieldInfo, error)`:
    * Parse a line into `MinVerFieldInfo`, returning an error if the line does not split into exactly four tokens.

* Implement `MinVerFieldInfoFromStructField(field reflect.StructField, depth int) MinVerFieldInfo`:
    * Build `Name` with depth dashes, `TypeStr` from `field.Type.String()`, `MinVer` from the `minver` tag or '0.0.0', and `YamlTag` from the `yaml` tag.

* Implement `(MinVerFieldInfo) Equals(other MinVerFieldInfo) bool` and `(MinVerFieldInfo) String() string`:
    * `Equals` returns true if all fields match.
    * `String` returns a space-separated string of all fields.

* Update all fields in `YAMLConfig` and nested structs to include `yaml` tags:
    * Add `yaml:"autocommit"` to `BehaviorYAMLConfig.AutoCommit`.
    * Add `yaml:"name"` and `yaml:"password"` to `UserYAMLConfig.Name` and `UserYAMLConfig.Password`.
    * Remove `DatabaseYAMLConfig` struct.

* Create `go/cmd/dolt/commands/sqlserver/testdata/minver_validation.txt`:
    * Include one `MinVerFieldInfo.String()` line per non-TBD field of `YAMLConfig`, in the order visited by `structwalk.Walk`.
    * Exclude fields with `minver` tag 'TBD'.
    * Allow comment lines starting with '#'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.