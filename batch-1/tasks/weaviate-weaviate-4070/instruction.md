Implement the necessary improvements to the cloud cluster transport layer. Move the utility function for obtaining a free TCP port to a shared utility package, update the gRPC server component's naming, enhance error messaging for client methods, and add comprehensive tests for the server component.

*   Move the MustGetFreeTCPPort function to the `cloud/utils/utils.go` file.
    *   Ensure it returns a positive integer representing a free TCP port.
    *   Ensure it panics on any network error.
*   Update the gRPC server component in `cloud/transport/service.go`.
    *   Rename the constructor to `New(ms members, ex executor, address string, l *slog.Logger) *Service`.
    *   Ensure the Service struct replaces the previous Cluster type.
    *   Implement the `Open()` method to:
        *   Return a non-nil error if the address is an empty string.
        *   Return an error wrapping a *net.OpError for invalid addresses.
        *   Return nil and start serving gRPC requests for valid TCP addresses.
    *   Implement the `Leader()` method to return the current leader address.
*   Enhance error handling in `cloud/transport/client.go`.
    *   Ensure `Client.Join()`, `Client.Notify()`, `Client.Remove()`, and `Client.Apply()` wrap address resolution failures with an error message containing "resolve".
    *   Ensure these methods wrap dial/connection failures with an error message containing "dial".
*   Implement error code mappings in `cloud/transport/service.go`.
    *   Map `store.ErrNotOpen` to gRPC status code `codes.Unavailable`.
    *   Map `store.ErrLeaderNotFound` to gRPC status code `codes.Internal`.
    *   Map `store.ErrNotLeader` to gRPC status code `codes.NotFound`.
*   Implement the `NewRPCResolver` function in the transport package.
    *   Ensure `NewRPCResolver(isIncremented bool, rpcPort int)` returns an `RPCResolver`.
    *   Implement the `Address` method to:
        *   Return a *net.AddrError if the input address lacks a colon separator.
        *   Use the fixed `rpcPort` if `isIncremented` is false.
        *   Return a *strconv.NumError for non-numeric ports if `isIncremented` is true.
        *   Increment numeric ports by 1 if `isIncremented` is true.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.