## Description

Biome's file filtering logic has a priority ordering bug: feature-level include lists (for the formatter, linter, or import organizer) can override global file exclusion rules. When a file is globally ignored under the top-level files configuration, users expect it to be completely excluded from all processing. However, if that same file also appears in the formatter's or linter's include list, Biome still processes it — contrary to the user's stated intent.

## Expected Behavior

- A file marked as globally ignored must be excluded from all processing, even if it is explicitly included at the formatter, linter, or import organizer level.
- Files excluded via a version control system's ignore file must also be excluded from formatting, even if they appear in a feature-level include list.
- Files with absolute paths that do not match any VCS ignore pattern should be processed normally without errors.
- Patterns listed in an override's ignore section must not prevent files from being processed globally — they should only affect how that specific override's settings apply to those files.

## Why This Matters

Users who globally ignore files expect those files to be truly excluded from all tool operations. The current behavior silently bypasses global exclusions when feature-level includes are present, leading to unexpected processing of files that should be skipped. Fixing this cascade ensures that global rules take precedence, making the configuration system predictable and consistent with user expectations.
