I'm working on the Gleam build tool's dependency management commands.

*   Must implement a function `pretty_print_outdated_versions` in `compiler-cli/src/dependencies.rs` that accepts a `usize` (total number of packages) and a map from package name strings to tuples of (current version, latest version), and returns a formatted string.

*   When the versions map is non-empty, the output must begin with a summary line in the exact format: "{count} of {total} packages have newer versions available.", followed by a blank line and a space-aligned table. For example, with 3 outdated packages out of 12 total: "3 of 12 packages have newer versions available.\n\n{table}".

*   When the versions map is empty, the output must be only the summary line followed by a newline: "0 of {total} packages have newer versions available.\n". No table should be produced.

*   The table must have column headers "Package", "Current", and "Latest", with a separator row of dashes beneath the headers matching each column's width. Columns must be space-padded so all entries in each column are left-aligned to the width of the widest entry.

*   Packages in the table must be sorted alphabetically by package name.

*   The snapshot file for the non-empty test must be created at `compiler-cli/src/dependencies/snapshots/gleam_cli__dependencies__tests__pretty_print_outdated_versions.snap` with content matching the exact output of `pretty_print_outdated_versions(12, {gleam_stdlib: (0.45.0, 0.46.0), wisp: (2.1.0, 2.1.1), very_long_package_name: (12.12.12, 120.12.12)})`.

*   The snapshot file for the empty test must be created at `compiler-cli/src/dependencies/snapshots/gleam_cli__dependencies__tests__pretty_print_outdated_versions_no_updates.snap` with content matching the exact output of `pretty_print_outdated_versions(12, {})` — that is, "0 of 12 packages have newer versions available.".


*   Interface details: Type: Function
Name: pretty_print_outdated_versions
Location: compiler-cli/src/dependencies.rs
Signature: pretty_print_outdated_versions(total_packages: usize, versions: dependency::PackageVersionDiffs) -> EcoString
Description: Formats and returns a string summarizing how many packages have newer versions available. `total_packages` is the total count of all packages being checked, and `versions` is a map from package name to a tuple of (current_version, latest_version). When `versions` is empty, returns only the summary line "0 of {total_packages} packages have newer versions available.\n". When `versions` is non-empty, returns the summary line followed by a blank line and a space-aligned table listing each outdated package with its current and latest versions, with packages sorted alphabetically. The table uses column headers "Package", "Current", and "Latest" with a row of dashes beneath each header matching that column's width.

Type: SnapshotFile
Name: gleam_cli__dependencies__tests__pretty_print_outdated_versions.snap
Location: compiler-cli/src/dependencies/snapshots/gleam_cli__dependencies__tests__pretty_print_outdated_versions.snap
Description: Insta snapshot file for the non-empty outdated versions test. Must contain the exact rendered output of pretty_print_outdated_versions(12, {gleam_stdlib: (0.45.0, 0.46.0), wisp: (2.1.0, 2.1.1), very_long_package_name: (12.12.12, 120.12.12)}). Expected content (after the insta header):
  3 of 12 packages have newer versions available.

  Package                 Current   Latest
  -------                 -------   ------
  gleam_stdlib            0.45.0    0.46.0
  very_long_package_name  12.12.12  120.12.12
  wisp                    2.1.0     2.1.1

Type: SnapshotFile
Name: gleam_cli__dependencies__tests__pretty_print_outdated_versions_no_updates.snap
Location: compiler-cli/src/dependencies/snapshots/gleam_cli__dependencies__tests__pretty_print_outdated_versions_no_updates.snap
Description: Insta snapshot file for the empty outdated versions test. Must contain the exact rendered output of pretty_print_outdated_versions(12, {}). Expected content (after the insta header):
  0 of 12 packages have newer versions available.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.