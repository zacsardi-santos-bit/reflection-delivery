I'm running into issues with combining upgrade flags in my dependency compilation workflow.

*   The upgrade settings must be represented as a structure with two separate fields: one for the upgrade strategy (which packages to consider for upgrade) and one for per-package version constraints. When displayed via the settings inspection command, this structure must appear with both fields visible, even when no upgrade options are specified.

*   When no upgrade flags are passed, the upgrade settings must display with a null strategy and an empty constraints map, rather than as a plain null/None value.

*   When the global upgrade flag is passed alone, the upgrade settings must display with 'All' as the strategy and an empty constraints map.

*   When the upgrade-all flag is combined with a package-specific upgrade flag that includes a version constraint (e.g., 'pkg<2'), all packages should be upgraded, but the specified package must be resolved to its latest version that satisfies the given constraint. The version constraint is stored separately from the upgrade strategy.

*   When the no-upgrade flag is combined with a package-specific upgrade flag (e.g., '--no-upgrade --upgrade-package click'), only the specified package must be upgraded while all other packages remain at their currently pinned versions. The output file header must include the '--no-upgrade' flag.

*   When the package-specific upgrade flag is used (without a global upgrade or no-upgrade flag), the upgrade settings must display with a 'Packages' strategy listing just the package names, and an empty constraints map. The packages are stored as a set of names, not as full requirement objects with specifier and marker details.

*   In the upgrade settings display, the 'Packages' strategy variant must show only package names (e.g., 'PackageName("pkg")') without any requirement metadata (no specifier, marker, source, or origin fields). The version constraints, when present, are stored separately in the constraints field.

*   When two upgrade configurations are combined (e.g., from config file and CLI), the strategy from the more explicit source (All or None) takes precedence, while version constraints from both sources are merged.


*   Interface details: Type: Struct
Name: Upgrade
Location: crates/uv-configuration/src/package_options.rs
Description: Represents the upgrade configuration, combining a strategy for which packages to upgrade with per-package version constraints. Previously an enum, now a struct with two fields.
Signature:
  - Field `strategy`: UpgradeStrategy (an enum with variants None, All, Packages(FxHashSet<PackageName>))
  - Field `constraints`: FxHashMap<PackageName, Vec<Requirement>>
  - Debug display format: `Upgrade { strategy: <UpgradeStrategy>, constraints: <map> }`
  - Default display: `Upgrade { strategy: None, constraints: {} }`

Type: Enum
Name: UpgradeStrategy
Location: crates/uv-configuration/src/package_options.rs
Description: Strategy for determining which packages to consider for upgrade. Previously the variants of the Upgrade enum, now a separate enum.
Signature:
  - Variant `None` (default): No packages upgraded
  - Variant `All`: All packages upgraded
  - Variant `Packages(FxHashSet<PackageName>)`: Only the specified packages are upgraded. Stores package names only (not full Requirement objects).

Type: Function
Name: from_args
Location: crates/uv-configuration/src/package_options.rs
Description: Constructs an Upgrade from CLI arguments. The behavior must follow these rules:
  - `--upgrade` alone → Upgrade { strategy: All, constraints: {} }
  - `--no-upgrade` alone → Upgrade { strategy: None, constraints: {} }
  - `--upgrade-package pkg` alone → Upgrade { strategy: Packages({PackageName("pkg")}), constraints: {} }
  - `--upgrade --upgrade-package pkg<constraint>` → Upgrade { strategy: All, constraints: {pkg: [Requirement with specifier]} }
  - `--no-upgrade --upgrade-package pkg` → Upgrade { strategy: Packages({PackageName("pkg")}), constraints: {} }
  - `--upgrade --upgrade-package pkg` (no version constraint) → Upgrade { strategy: All, constraints: {} } (empty specifiers are skipped in constraints)
  - No flags, no packages → returns None (not Some(Upgrade))
Signature: from_args(upgrade: Option<bool>, upgrade_package: Vec<Requirement>) -> Option<Upgrade>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.