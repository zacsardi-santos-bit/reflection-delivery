Update the journal system to use descriptive field names for pipe creation events, ensuring clarity and consistency in serialization and deserialization processes. Implement the changes in the specified source files to reflect the new field names.

*   Modify the `CreatePipeV1` variant of the `JournalEntry` enum:
    *   Change field names to `read_fd` and `write_fd` from `fd1` and `fd2`.
    *   Ensure the `read_fd` represents the read end and `write_fd` represents the write end of the pipe.
    *   Location: `lib/journal/src/entry.rs`
    *   Signature: `CreatePipeV1 { read_fd: Fd, write_fd: Fd }`

*   Update the serialization struct `JournalEntryCreatePipeV1`:
    *   Rename fields to `read_fd` and `write_fd` from `fd1` and `fd2`.
    *   Ensure fields are of type `u32`.
    *   Location: `lib/journal/src/concrete/archived.rs`
    *   Signature: `pub struct JournalEntryCreatePipeV1 { pub read_fd: u32, pub write_fd: u32 }`

*   Adjust deserialization logic:
    *   Ensure correct mapping from serialized representation back to `CreatePipeV1` using `read_fd` and `write_fd`.
    *   Location: `lib/journal/src/concrete/archived_from.rs`

*   Verify serialization/deserialization roundtrip:
    *   Ensure a `CreatePipeV1` journal entry with specific `read_fd` and `write_fd` values matches the original after a complete roundtrip.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.