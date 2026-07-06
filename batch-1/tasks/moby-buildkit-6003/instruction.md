Implement a "raw" socket forwarding mode in BuildKit to allow direct forwarding of Unix domain sockets into build containers without SSH agent protocol wrapping. Update the command-line tool and programmatic API to support this mode, ensuring proper validation and error handling.

*   Update the `AgentConfig` struct in the `sshprovider` package:
    *   Add a `Raw` bool field to enable raw socket forwarding.

*   Modify the `ParseSSH` function in `cmd/buildctl/build/ssh.go`:
    *   Recognize 'raw=true' and 'raw=false' as key-value options in the SSH config entry.
    *   Ensure these options can appear in any order with the socket path.
    *   Return an error with the message 'raw mode must supply exactly one socket path' if `raw=true` is specified with zero paths.

*   Update the `NewSSHAgentProvider` function in `session/sshforward/sshprovider/agentprovider.go`:
    *   Return an error with the message 'raw mode only supported with socket paths' if `Raw` is true and the path is not a Unix domain socket.
    *   Return a non-nil `session.Attachable` with no error if `Raw` is true and the path is a valid Unix domain socket.

*   Implement the following in the `sshprovider` package:
    *   Create an unexported type `dialerFn` in `session/sshforward/sshprovider/raw_provider.go`:
        *   Define it as a function type with the signature `func(ctx context.Context) (net.Conn, error)`.
    *   Create an unexported struct `socketProvider` in `session/sshforward/sshprovider/raw_provider.go`:
        *   Include a field `m` of type `map[string]dialerFn`.
        *   Implement a `Register` method to register the SSH gRPC service on a `*grpc.Server`.
        *   Implement `CheckAgent` to look up the request ID in `m` and return an error if not found, including the requested ID in the error message.
        *   Implement `ForwardAgent` to extract the SSH ID from gRPC metadata, dial the corresponding `dialerFn`, and proxy bytes bidirectionally between the gRPC stream and the connection, handling multiple sequential and large messages correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.