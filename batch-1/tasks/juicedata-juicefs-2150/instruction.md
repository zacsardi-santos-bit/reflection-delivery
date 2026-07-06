Implement the necessary changes to the JuiceFS object storage layer to improve test suite maintainability and add support for new cloud storage providers. Update the code to read configuration from environment variables and remove obsolete providers.

*   Implement new cloud storage provider functions in the `pkg/object` package:
    *   Define `newEos(endpoint, accessKey, secretKey string) (ObjectStorage, error)` to create an EOS client.
    *   Define `newWasabi(endpoint, accessKey, secretKey string) (ObjectStorage, error)` to create a Wasabi client.
    *   Define `newSCS(endpoint, accessKey, secretKey string) (ObjectStorage, error)` to create an SCS client.
    *   Define `newIBMCOS(endpoint, accessKey, secretKey string) (ObjectStorage, error)` to create an IBM Cloud Object Storage client.

*   Update SQL driver registrations in `pkg/object/sql.go`:
    *   Import SQLite3 driver with `_ "github.com/mattn/go-sqlite3"`.
    *   Import MySQL driver with `_ "github.com/go-sql-driver/mysql"`.
    *   Import PostgreSQL driver with `_ "github.com/lib/pq"`.

*   Remove obsolete storage providers:
    *   Delete `pkg/object/mss.go` or relocate its types to avoid conflicts.
    *   Delete `pkg/object/yovole.go`.

*   Modify cloud provider tests to use environment variables:
    *   Ensure tests skip immediately using `t.SkipNow()` if the relevant endpoint environment variable is not set.

*   Implement a `TestMain` function in the test file:
    *   Read from `/tmp/aksk.txt` if it exists.
    *   Parse and set environment variables from each line formatted as `SCHEMA KEY1=VALUE1 KEY2=VALUE2 ...`.
    *   Call `m.Run()` to execute tests.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.