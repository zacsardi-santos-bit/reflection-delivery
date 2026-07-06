Implement a case-insensitive concurrent map utility for managing project names in a workflow management system. Ensure project names are unique regardless of capitalization, and configure the database to support case-insensitive string comparisons.

*   Implement the `CaseInsensitiveConcurrentHashMap` class in `azkaban-common/src/main/java/azkaban/utils/CaseInsensitiveConcurrentHashMap.java`.
    *   Normalize all keys to lowercase for `put`, `get`, `containsKey`, and `remove` operations.
    *   Ensure `put` overwrites existing values when keys differ only in case.
    *   Ensure `containsKey` returns true for any casing of a stored key.
    *   Ensure `get` and `remove` return null for non-existent keys.
    *   Method signatures:
        *   `put(String key, V value) -> V`
        *   `get(String key) -> V`
        *   `containsKey(String key) -> boolean`
        *   `remove(String key) -> V`

*   Update the `ProjectManager` class in `azkaban-common/src/main/java/azkaban/project/ProjectManager.java`.
    *   Constructor must accept parameters in the order: `AzkabanProjectLoader`, `ProjectLoader`, `StorageManager`, `Props`.
    *   Implement `createProject(String projectName, String description, User creator) -> Project`.
        *   Throw `ProjectManagerException` with "Project already exists." if a project name exists case-insensitively.

*   Update the `AzkabanProjectLoader` class in `azkaban-common/src/main/java/azkaban/project/AzkabanProjectLoader.java`.
    *   Constructor must accept parameters in the order: `Props`, `ProjectLoader`, `StorageManager`, `FlowLoaderFactory`.

*   Modify `JdbcProjectImpl` (a `ProjectLoader` implementation).
    *   Ensure `createNewProject` throws `ProjectManagerException` with "Active project with name {NAME} already exists in db." for duplicate project names case-insensitively.

*   Configure H2 database connections for case-insensitive comparisons.
    *   Append ";IGNORECASE=TRUE" to JDBC URLs for all H2 test data sources:
        *   `azkaban-common` test: "jdbc:h2:mem:test;IGNORECASE=TRUE"
        *   `azkaban-db` test: "jdbc:h2:mem:test;IGNORECASE=TRUE"
        *   `azkaban-web-server` quartz test properties: "jdbc:h2:mem:test;IGNORECASE=TRUE"

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.