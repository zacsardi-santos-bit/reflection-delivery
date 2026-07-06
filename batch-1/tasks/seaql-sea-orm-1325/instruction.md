Implement support for formatted UUID types as entity model field types in the ORM. Ensure that UUIDs in braced, hyphenated, simple, and URN formats can be inserted and retrieved from the database without manual conversion. Enable support for vectors of these formatted UUID types in PostgreSQL array columns.

*   Implement TryGetable trait for formatted UUID types:
    *   For `uuid::fmt::Braced`:
        *   Location: `src/executor/query.rs`
        *   Signature: `fn try_get(res: &QueryResult, pre: &str, col: &str) -> Result<uuid::fmt::Braced, TryGetError>`
        *   Convert using `uuid::Uuid::braced()`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.
    *   For `uuid::fmt::Hyphenated`:
        *   Location: `src/executor/query.rs`
        *   Signature: `fn try_get(res: &QueryResult, pre: &str, col: &str) -> Result<uuid::fmt::Hyphenated, TryGetError>`
        *   Convert using `uuid::Uuid::hyphenated()`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.
    *   For `uuid::fmt::Simple`:
        *   Location: `src/executor/query.rs`
        *   Signature: `fn try_get(res: &QueryResult, pre: &str, col: &str) -> Result<uuid::fmt::Simple, TryGetError>`
        *   Convert using `uuid::Uuid::simple()`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.
    *   For `uuid::fmt::Urn`:
        *   Location: `src/executor/query.rs`
        *   Signature: `fn try_get(res: &QueryResult, pre: &str, col: &str) -> Result<uuid::fmt::Urn, TryGetError>`
        *   Convert using `uuid::Uuid::urn()`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.

*   Implement TryGetable trait for vectors of formatted UUID types in PostgreSQL:
    *   For `Vec<uuid::fmt::Braced>`:
        *   Location: `src/executor/query.rs` (inside `postgres_array` module)
        *   Signature: `fn try_get(res: &QueryResult, pre: &str, col: &str) -> Result<Vec<uuid::fmt::Braced>, TryGetError>`
        *   Convert each element using `uuid::Uuid::braced()`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.
    *   For `Vec<uuid::fmt::Hyphenated>`:
        *   Location: `src/executor/query.rs` (inside `postgres_array` module)
        *   Signature: `fn try_get(res: &QueryResult, pre: &str, col: &str) -> Result<Vec<uuid::fmt::Hyphenated>, TryGetError>`
        *   Convert each element using `uuid::Uuid::hyphenated()`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.
    *   For `Vec<uuid::fmt::Simple>`:
        *   Location: `src/executor/query.rs` (inside `postgres_array` module)
        *   Signature: `fn try_get(res: &QueryResult, pre: &str, col: &str) -> Result<Vec<uuid::fmt::Simple>, TryGetError>`
        *   Convert each element using `uuid::Uuid::simple()`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.
    *   For `Vec<uuid::fmt::Urn>`:
        *   Location: `src/executor/query.rs` (inside `postgres_array` module)
        *   Signature: `fn try_get(res: &QueryResult, pre: &str, col: &str) -> Result<Vec<uuid::fmt::Urn>, TryGetError>`
        *   Convert each element using `uuid::Uuid::urn()`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.

*   Update existing TryGetable implementations:
    *   For `uuid::Uuid`:
        *   Replace `try_getable_all!(uuid::Uuid)` with an explicit implementation.
        *   Convert using `Into::into`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.
    *   For `Vec<uuid::Uuid>`:
        *   Replace `try_getable_postgres_array!(uuid::Uuid)` with an explicit implementation.
        *   Convert each element using `Into::into`.
        *   Gated with `#[cfg(feature = "with-uuid")]`.

*   Ensure entity models with formatted UUID fields support correct insert and retrieve operations.
*   Ensure `Vec<uuid::fmt::Hyphenated>` fields are usable as Postgres array columns.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.