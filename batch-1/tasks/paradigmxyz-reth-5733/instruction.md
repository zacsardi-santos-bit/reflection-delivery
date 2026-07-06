Simplify the snapshot filename generation and parsing by removing the transaction range from the filename format. Update the methods to use only the block range for identifying snapshots, and ensure the parser handles filenames correctly.

*   Update the `filename` method in `SnapshotSegment`:
    *   Accept only a block range (`&RangeInclusive<BlockNumber>`) as a parameter.
    *   Return a `String` in the format 'snapshot_{segment}_{block_start}_{block_end}'.
    *   Remove the transaction range parameter from the method signature.

*   Update the `filename_with_configuration` method in `SnapshotSegment`:
    *   Accept filters, compression, and a block range only.
    *   Remove the transaction range parameter from the method signature.
    *   Continue to append filter and compression identifiers to the base filename.

*   Update the `parse_filename` method in `SnapshotSegment`:
    *   Change the parameter type to accept a `&str` instead of an `OsStr`.
    *   Return `Option<(SnapshotSegment, RangeInclusive<BlockNumber>)>` without a transaction range.
    *   Ensure the method parses filenames in the format 'snapshot_{segment}_{block_start}_{block_end}'.
    *   Return `None` for filenames missing valid block_start and block_end numbers.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.