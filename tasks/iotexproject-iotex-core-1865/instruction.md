Add a new command to the ioctl command-line tool to query reward information from the IoTeX blockchain. Implement functionality to display the overall reward pool status or unclaimed rewards for a specific delegate based on the provided arguments.

*   Implement the `NewNodeRewardCmd` function in `ioctl/newcmd/node/nodereward.go`.
    *   Accept an `ioctl.Client` parameter and return a `*cobra.Command`.
    *   Allow the command to accept 0 or 1 arguments using `cobra.MaximumNArgs(1)`.

*   When the command is executed without arguments:
    *   Query the rewarding protocol's `AvailableBalance` using `ReadState` with:
        *   `ProtocolID []byte("rewarding")`
        *   `MethodName []byte("AvailableBalance")`
        *   No `Arguments` field.
    *   Query the rewarding protocol's `TotalBalance` using `ReadState` with:
        *   `ProtocolID []byte("rewarding")`
        *   `MethodName []byte("TotalBalance")`
        *   No `Arguments` field.

*   When the command is executed with one argument:
    *   Use `client.Address()` to resolve the argument to an address string.
    *   Query the rewarding protocol's `UnclaimedBalance` using `ReadState` with:
        *   `ProtocolID []byte("rewarding")`
        *   `MethodName []byte("UnclaimedBalance")`
        *   `Arguments [][]byte{[]byte(resolvedAddress)}`.

*   Ensure the command integrates with the existing ioctl client infrastructure:
    *   Use `client.APIServiceClient(ioctl.APIServiceConfig{Endpoint: endpoint, Insecure: insecure})` to obtain the API service client for `ReadState` calls.

*   Extend the Client interface in `ioctl/client.go`:
    *   Add `Address(in string) (string, error)` method to resolve an input string to a valid IoTeX address.

*   Update the mock client in `test/mock/mock_ioctlclient/mock_ioctlclient.go`:
    *   Implement the `Address(in string) (string, error)` mock method for both `MockClient` and `MockClientMockRecorder`.

*   Ensure both command invocations (with and without an address argument) complete without error and return a non-nil command result.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.