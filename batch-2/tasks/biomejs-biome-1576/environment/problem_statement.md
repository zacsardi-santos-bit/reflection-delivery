## Description

When running the Prettier-to-Biome migration tool, the tool correctly translates Prettier formatting settings into equivalent Biome configuration options. However, it does not currently process Prettier's ignore file, which means any paths or glob patterns that were excluded from Prettier formatting are silently dropped during migration.

Developers commonly exclude directories like build artifacts, generated files, and third-party packages from Prettier. After migration, none of those exclusions are carried over to Biome, leaving the new Biome configuration incomplete and requiring manual intervention.

## Expected Behavior

- When a Prettier ignore file is present alongside Prettier configuration, the migration command should automatically detect and read it.
- Non-empty, non-comment lines from the ignore file should be added to the formatter's exclude list in the resulting Biome configuration, preserving the original file order.
- Comment lines (those beginning with a hash character) and blank lines should be skipped and not included in the migration output.
- Both dry-run (preview) mode and write mode should reflect the ignore patterns in the proposed or written Biome configuration.

## Why This Matters

Without this, migrating a project that relies on ignore rules requires manual follow-up work. Supporting automatic ignore file migration makes the tool more complete and reduces friction for developers adopting Biome.
