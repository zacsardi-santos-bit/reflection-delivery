Implement a structured error response in the Hrana stream protocol to include both a human-readable message and a machine-readable error code when a SQLite error occurs. Ensure that the error code and message are included in the Hrana Error structure and properly wrapped in the StreamError variant for stream-level errors.

*   Update the Hrana Error structure:
    *   Add a `code` field of type `String` to represent the machine-readable error code.
    *   Ensure both `message` and `code` fields are public and included in the Rust Debug output.
    *   Define the structure in `libsql-sys/src/hrana/proto.rs` or the equivalent shared Hrana proto module.

*   Modify the StreamError variant:
    *   Ensure it wraps the Hrana Error struct as a tuple variant: `StreamError(Error { message, code })`.
    *   Confirm that the Debug representation is `Hrana(StreamError(Error { message: "...", code: "..." }))`.
    *   Implement this in `libsql-server/src/hrana/http/stream.rs`.

*   Handle specific SQLite errors:
    *   For a UNIQUE constraint violation, set the `code` field to "SQLITE_CONSTRAINT".
    *   Format the `message` field for a UNIQUE constraint violation as "SQLite error: UNIQUE constraint failed: <table>.<column>".
    *   Ensure that the error response includes both the `message` and `code` fields.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.