Update the peer-to-peer networking library references in the blockchain node project to reflect recent changes in package structure and method naming. Ensure all code that converts peer identifiers to strings uses the updated method, and modify the module dependency file to use the new library package and version.

*   Update import paths in all relevant files:
    *   Replace `github.com/libp2p/go-libp2p-core/peer` with `github.com/libp2p/go-libp2p/core/peer` in:
        *   `nodeinfo/manager.go`
        *   `p2p/agent.go`
        *   `blocksync/blocksync.go`
        *   `chainservice/chainservice.go`
        *   `dispatcher/dispatcher.go`
        *   `dispatcher/subscriber.go`
        *   `pkg/messagebatcher/batchwriter.go`
*   Modify method calls for peer ID string conversion:
    *   Replace `.Pretty()` with `.String()` in:
        *   `nodeinfo/manager.go`:
            *   Use `peer.ID.String()` in `BroadcastNodeInfo`, `RequestSingleNodeInfoAsync`, and `HandleNodeInfoRequest`.
        *   `p2p/agent.go`:
            *   Use `rawmsg.GetFrom().String()` and `peerInfo.ID.String()` in `Start`.
            *   Use `peer.ID.String()` in `UnicastOutbound` for `peerName` and metrics labels.
        *   `blocksync/blocksync.go`:
            *   Use `peer.ID.String()` in `requestBlock` error logs.
*   Update module dependency:
    *   In `go.mod`, replace `github.com/libp2p/go-libp2p-core v0.8.5` with `github.com/libp2p/go-libp2p v0.32.2` or a compatible version.
    *   Update `go.sum` with the new checksums.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.