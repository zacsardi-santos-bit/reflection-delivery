Simplify the configuration management API by updating the constructor to remove unnecessary parameters, ensuring reliable get/set operations, and improving the callback mechanism for configuration changes. Implement the following requirements to achieve these objectives.

*   Update the `NewManager` function:
    *   Modify the signature to `NewManager(kv kvdb.Kvdb) (ConfigManager, error)` in the `osdconfig` package.
    *   Ensure it only requires a kvdb handle and returns a `ConfigManager` and an error.

*   Implement `ConfigManager` interface methods:
    *   `SetClusterConf(config *ClusterConfig) error`: Persist the cluster configuration to the backend kvdb store and return an error on failure.
    *   `GetClusterConf() (*ClusterConfig, error)`: Retrieve and return a `*ClusterConfig` that is deep-equal to what was set, along with an error.
    *   `SetNodeConf(config *NodeConfig) error`: Accept a `*NodeConfig` with a non-empty `NodeId` field, persist it to the backend kvdb store, and return an error on failure.
    *   `GetNodeConf(nodeID string) (*NodeConfig, error)`: Retrieve and return a `*NodeConfig` that is deep-equal to what was set, using the provided node ID, along with an error.
    *   `WatchCluster(name string, cb func(config *ClusterConfig) error) error`: Register the callback function to be invoked with the new configuration when a cluster config is written.
    *   `WatchNode(name string, cb func(config *NodeConfig) error) error`: Register the callback function to be invoked with the new configuration when a node config is written.

*   Ensure callback invocation:
    *   When `SetClusterConf` is called, invoke each registered cluster watcher callback with the updated `*ClusterConfig`.
    *   When `SetNodeConf` is called, invoke each registered node watcher callback with the updated `*NodeConfig`.

*   Define required data structures in the `osdconfig` package:
    *   `ClusterConfig` with fields `ClusterId string` and `Driver string`.
    *   `NodeConfig` with fields `NodeId string`, `Storage *StorageConfig`, and `Network *NetworkConfig`.
    *   `StorageConfig` with field `Devices []string`.
    *   `NetworkConfig` with field `DataIface string`.
    *   `NodesConfig` with field `NodeConf map[string]*NodeConfig`.

*   Implement a package-internal helper function:
    *   `newInMemKvdb() (kvdb.Kvdb, error)`: Create and return an in-memory kvdb instance, accessible within the `osdconfig` package.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.