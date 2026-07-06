I'm working on a Dolt cluster setup where one node acts as the primary and others are standbys.

*   The CommitHook interface must include a new method ExecuteForReplicaWrite() bool that determines whether a hook should be executed when a write arrives through the replica (standby) write path via the remotesapi endpoint.

*   All existing implementors of CommitHook must provide the ExecuteForReplicaWrite() bool method to satisfy the interface.

*   The hooksFiringRemoteSrvStore struct must include a replicaWrite bool field. Its struct literal initialization must use named fields (RemoteSrvStore, ddb, replicaWrite).

*   When hooksFiringRemoteSrvStore is constructed with replicaWrite: true and its Commit() method is called, only commit hooks that return true from ExecuteForReplicaWrite() must be executed; hooks that return false from ExecuteForReplicaWrite() must be skipped entirely (zero calls).

*   When hooksFiringRemoteSrvStore is constructed with replicaWrite: false (the default), all hooks fire as before, regardless of their ExecuteForReplicaWrite() return value.

*   The Server struct in the go-sql-server-driver driver package must add a LogNotMatches field of type []string with YAML tag log_not_matches.

*   The MakeServer function must iterate over s.LogNotMatches after processing s.LogMatches and assert that each pattern in LogNotMatches does NOT match the server log output.

*   In the cluster integration test YAML, when log_not_matches is specified, the server test must fail if the log output matches the pattern 'cluster/commithook received commit callback for a commit on .*, but we are not role primary'.


*   Interface details: Type: Interface
Name: CommitHook
Location: go/libraries/doltcore/doltdb/hooksdatabase.go
Description: Interface for commit hooks. Must add the new ExecuteForReplicaWrite method alongside the existing Execute and ExecuteForWorkingSets methods. All structs that implement CommitHook must provide this method.
Signature: ExecuteForReplicaWrite() bool

---

Type: Struct field
Name: hooksFiringRemoteSrvStore
Location: go/libraries/doltcore/sqle/remotesrv.go
Description: Struct wrapping a RemoteSrvStore with commit hook firing logic. Must gain a new replicaWrite bool field. Struct literals must use named fields. When replicaWrite is true, the Commit() method must only fire hooks where ExecuteForReplicaWrite() returns true; hooks returning false must be skipped entirely (0 calls). When replicaWrite is false (default), all hooks fire as before.
Fields:
  RemoteSrvStore remotesrv.RemoteSrvStore
  ddb            *doltdb.DoltDB
  replicaWrite   bool

---

Type: Struct field
Name: Server.LogNotMatches
Location: go/libraries/doltcore/dtestutils/sql_server_driver/server.go
Description: New field added to the Server struct. Contains a list of regex patterns that must NOT appear in the server's log output after successful termination. Complementary to the existing LogMatches field.
Signature: LogNotMatches []string `yaml:"log_not_matches"`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.