Move the specified storage and search sub-packages for image vulnerability data and image data out of their restricted directories to make them accessible throughout the codebase. Update all import paths accordingly and ensure that integration tests can fully utilize these packages.

*   Relocate CVE image datastore sub-packages:
    *   Move `central/cve/image/datastore/internal/search/` to `central/cve/image/datastore/search/`.
    *   Move `central/cve/image/datastore/internal/store/` to `central/cve/image/datastore/store/`.

*   Relocate image datastore sub-packages:
    *   Move `central/image/datastore/internal/search/` to `central/image/datastore/search/`.
    *   Move `central/image/datastore/internal/store/` to `central/image/datastore/store/`.

*   Update import paths:
    *   Modify all import statements in production code such as `datastore.go`, `datastore_impl.go`, `singleton.go`, and others to reflect the new package paths.

*   Ensure functionality of specific packages and functions:
    *   `central/image/datastore/store/common` must export split/merge image functionality for `TestSplitAndMergeImage`.
    *   `central/cve/image/datastore/search` package's `New` function should accept a store and an indexer, returning a searcher.
    *   `central/cve/image/datastore/store/postgres` package's `New` function should accept a `*pgxpool.Pool` and return a store; `NewIndexer` should accept a `*pgxpool.Pool` and return an indexer.
    *   `central/image/datastore/store/postgres` package's `Destroy` function should accept a context and a `*pgxpool.Pool` to drop database tables.

*   Implement the CVE unsuppression loop:
    *   `NewLoop` in `central/cve/suppress` should accept CVE store arguments and return a `CVEUnsuppressLoop` with the concrete type `*cveUnsuppressLoopImpl`.
    *   `cveUnsuppressLoopImpl` should have an `unsuppressCVEsWithExpiredSuppressState` method to query and unsuppress expired CVEs.

*   Ensure the CVE datastore constructor:
    *   `New` function in `central/cve/image/datastore` should accept a store, an indexer, and a searcher, with the searcher created using the `New` function from `central/cve/image/datastore/search`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.