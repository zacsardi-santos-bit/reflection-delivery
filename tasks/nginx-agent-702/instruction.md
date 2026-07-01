Implement a response flow for configuration upload requests in the nginx agent, ensuring that the management plane receives status updates. Update the file management component to perform file operations only after confirming a connection to the management plane.

*   Update the CommandPlugin:
    *   Subscribe to the DataPlaneResponseTopic in addition to existing topics.
    *   Process messages on the DataPlaneResponseTopic by calling `SendDataPlaneResponse` on the command service with the DataPlaneResponse payload.
    *   Publish full ManagementPlaneRequest messages to ConfigUploadRequestTopic when received.

*   Modify the CommandService:
    *   Ensure `NewCommandService` initiates a subscribe call upon construction.
    *   Update `UpdateDataPlaneStatus` to return `(*mpi.CreateConnectionResponse, error)`.
    *   Ensure `createConnection` returns `(*mpi.CreateConnectionResponse, error)`.
    *   Implement `SendDataPlaneResponse(ctx context.Context, response *mpi.DataPlaneResponse) error` to send responses via the subscribe client.
    *   Include `subscribeClient` and `subscribeClientMutex` fields in the CommandService struct.

*   Enhance the FilePlugin:
    *   Subscribe to the ConnectionCreatedTopic before other topics.
    *   Mark the file manager service as connected upon processing messages on ConnectionCreatedTopic.
    *   Ensure messages processed on ConfigUploadRequestTopic are of type `*mpi.ManagementPlaneRequest`.

*   Update the FileManagerService:
    *   Implement `SetIsConnected(connected bool)` to control file operation execution based on connection status.

*   Modify NginxConfigParser:
    *   Respect the AllowedDirectories list from the agent configuration when parsing config files.

*   Add a new constant:
    *   Define `ConnectionCreatedTopic` with the value 'connection-created' in `internal/bus/topics.go`.

*   Implement helper function:
    *   Create `OKDataPlaneResponse()` in `test/protos/data_plane_response.go` to return a `*mpi.DataPlaneResponse` with COMMAND_STATUS_OK and a populated MessageMeta.

*   Update interfaces and fakes:
    *   Modify the `commandService` interface in `internal/command/command_plugin.go` to include `SendDataPlaneResponse` and update `UpdateDataPlaneStatus`.
    *   Update `fake_command_service.go` to implement the new interface methods and return types.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.