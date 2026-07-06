Implement a connector service that allows external clients to route raw RPC calls through a node. Ensure the service can be toggled via configuration, persists its state, and handles RPC requests correctly.

*   Define a `ConnectorConfig` struct in `params/config.go` with:
    *   A field `Enabled` of type `bool`.
*   Update `NodeConfig` in `params/config.go` to include:
    *   A field `ConnectorConfig` of type `ConnectorConfig`.

*   Implement the `Service` struct in `services/connector/service.go` with:
    *   Unexported fields `rpcClient *rpc.Client` and `connectorSrvc *Service`.

*   Create the `NewService` function in `services/connector/service.go`:
    *   Signature: `NewService(rpcClient *rpc.Client, connectorSrvc *Service) *Service`.
    *   Return a `*Service` with `rpcClient` and `connectorSrvc` fields set to the provided arguments.

*   Implement the `Start` method for `Service`:
    *   Signature: `(s *Service) Start() error`.
    *   Ensure it returns a nil error.

*   Implement the `Stop` method for `Service`:
    *   Signature: `(s *Service) Stop() error`.
    *   Ensure it returns a nil error.

*   Implement the `APIs` method for `Service`:
    *   Signature: `(s *Service) APIs() []gethrpc.API`.
    *   Return a slice with one RPC API descriptor:
        *   `Namespace` set to "connector".
        *   `Version` set to "0.1.0".
        *   A non-nil `Service` value.

*   Implement the `Protocols` method for `Service`:
    *   Signature: `(s *Service) Protocols() []p2p.Protocol`.
    *   Ensure it returns nil.

*   Implement the `API` struct in `services/connector/api.go` with:
    *   An unexported field `s *Service`.

*   Implement the `CallRPC` method for `API`:
    *   Signature: `(api *API) CallRPC(inputJSON string) (string, error)`.
    *   Ensure it proxies a raw JSON-RPC request string to the RPC client.
    *   Return a non-empty string and nil error.
    *   For valid Ethereum methods, the response must not contain "does not exist/is not available".
    *   For unknown/unsupported methods, the response must contain "does not exist/is not available".
    *   For an empty input string, the response must be non-empty and must not contain "does not exist/is not available".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.