I'm working on hardening the integrity verification in our package manager.

*   The Install struct must include a new boolean field named update_checksums that defaults to false.

*   The InstallError enum must include a new variant named FrozenLockfileWithUpdateChecksums.

*   When Install is invoked with both frozen_lockfile set to true and update_checksums set to true, the run() method must return Err(InstallError::FrozenLockfileWithUpdateChecksums) — these two modes are mutually incompatible.

*   The PickPackageOptions struct must include a new boolean field named update_checksums that defaults to false.

*   When a lockfile contains a wrong checksum and update_checksums is false (the default), a non-frozen install must fail with a checksum error rather than silently continuing.

*   When a lockfile contains a wrong checksum and update_checksums is false, running an install with the force flag must still fail with a checksum error — the force flag is not a bypass for integrity checks.

*   When a lockfile contains a wrong checksum and update_checksums is true, the install must succeed and update the lockfile to record the correct integrity value.


*   Interface details: Type: Struct Field
Name: update_checksums
Location: pacquet/crates/package-manager/src/install.rs
Signature: pub update_checksums: bool
Description: New field on the Install struct. Must be set to false for normal installs. When true, the run() method will refresh locked integrity values from the registry. Conflicts with frozen_lockfile=true.

Type: Enum Variant
Name: FrozenLockfileWithUpdateChecksums
Location: pacquet/crates/package-manager/src/install.rs
Description: New variant of the InstallError enum. Returned by Install::run() when both frozen_lockfile and update_checksums are simultaneously set to true. These two modes are mutually incompatible.

Type: Struct Field
Name: update_checksums
Location: pacquet/crates/resolving-npm-resolver/src/pick_package.rs
Signature: pub update_checksums: bool
Description: New field on the PickPackageOptions struct. Must default to false. When true, bypasses the on-disk exact-version fast path so cached metadata cannot satisfy the call without a fresh conditional registry request.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.