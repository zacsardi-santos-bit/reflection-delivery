Implement a lifecycle-managed client for the pd-ctl tool to communicate with the PD server over HTTP. Create a globally accessible client variable and setup function to manage client initialization and cleanup. Update cluster subcommands to use typed methods for querying cluster information and status, and ensure test helpers properly initialize and close the client.

*   Expose a package-level exported variable named `PDCli` in `tools/pd-ctl/pdctl/command/global.go`:
    *   Type: `github.com/tikv/pd/client/http.Client`
    *   Must be accessible from external packages and support a `Close()` method.

*   Implement `SetNewPDClient` function in `tools/pd-ctl/pdctl/command/global.go`:
    *   Signature: `SetNewPDClient(addrs []string, opts ...pd.ClientOption)`
    *   Initialize `PDCli` with a new PD HTTP client using the provided server addresses and options.
    *   Close any existing client if `PDCli` is non-nil before creating a new one.
    *   Internally configure the client with logger redirection at 'fatal' level using `WithLoggerRedirection`.

*   Update the PD HTTP client interface in `client/http/interface.go`:
    *   Add `GetCluster` method: `GetCluster(ctx context.Context) (*metapb.Cluster, error)`
    *   Add `GetClusterStatus` method: `GetClusterStatus(ctx context.Context) (*ClusterState, error)`

*   Define `ClusterState` struct in `client/http/types.go`:
    *   Fields: `RaftBootstrapTime` (time.Time, JSON key 'raft_bootstrap_time', omitempty), `IsInitialized` (bool, JSON key 'is_initialized'), `ReplicationStatus` (string, JSON key 'replication_status').

*   Implement `WithLoggerRedirection` function in `client/http/client.go`:
    *   Signature: `WithLoggerRedirection(logLevel, fileName string) ClientOption`
    *   Configure the client's logger with the specified log level, creating no log file if `fileName` is empty.

*   Update the cluster command in `tools/pd-ctl`:
    *   Use `PDCli.GetCluster` to retrieve and display cluster information.
    *   Use `PDCli.GetClusterStatus` to retrieve and display cluster status.
    *   Output results as indented JSON.

*   Modify `ExecuteCommand` test helper in `tools/pd-ctl/tests/helper.go`:
    *   Call `SetNewPDClient` with a slice containing `args[1]` (PD server address) before executing the root command.
    *   Defer `PDCli.Close()` to ensure cleanup after command execution.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.