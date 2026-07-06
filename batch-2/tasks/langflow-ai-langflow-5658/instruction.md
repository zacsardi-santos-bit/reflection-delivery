Implement a mechanism to manage and prune vertex build records in the Langflow database to prevent unbounded growth. Ensure that both global and per-vertex limits are configurable and enforce these limits by deleting the oldest records when necessary. Allow for optional overrides of these limits during the logging process.

*   Update the `log_vertex_build` function in `src/backend/base/langflow/services/database/models/vertex_builds/crud.py`:
    *   Accept an `AsyncSession` and `VertexBuildBase` as positional arguments.
    *   Include optional keyword-only arguments: `max_builds_to_keep` (int | None, default None) and `max_builds_per_vertex` (int | None, default None).
    *   Return a `VertexBuildTable` instance with:
        *   `id` matching `vertex_build.id`.
        *   `flow_id` matching `vertex_build.flow_id`.
        *   `build_id` that is non-None and auto-generated, ensuring uniqueness.
    *   When `max_builds_to_keep` is None, retrieve the global limit from `settings.max_vertex_builds_to_keep`. Use the provided value if available.
    *   When `max_builds_per_vertex` is None, retrieve the per-vertex limit from `settings.max_vertex_builds_per_vertex`. Use the provided value if available.
    *   Ensure the total number of rows in `VertexBuildTable` does not exceed `max_builds_to_keep`. Delete the oldest records by timestamp if necessary.
    *   Ensure the number of rows in `VertexBuildTable` with the same `flow_id` and `id` does not exceed `max_builds_per_vertex`. Delete the oldest records for that vertex if necessary.
    *   Retain the newest builds and remove the oldest when limits are exceeded, maintaining records in strictly descending timestamp order.
    *   Handle multiple concurrent invocations without exceptions, ensuring the total count does not exceed the global limit after completion.

*   Update the `Settings` class in `src/backend/base/langflow/services/settings/base.py`:
    *   Add the field `max_vertex_builds_per_vertex` with type `int` and a default value of 2.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.