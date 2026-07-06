Refactor the Tendermint private validator into a modular design, enabling signing logic to run in a separate process communicating over a network socket. Implement the necessary functions and structures to support this modularization, ensuring secure and efficient operation.

*   Export the `TimeFormat` constant in `types/canonical_json.go` for use in the new subpackage.
    *   Ensure its value equals `wire.RFC3339Millis`.

*   Implement JSON-based validator functions in `types/priv_validator/json.go`:
    *   `GenPrivValidatorJSON(filePath string) *PrivValidatorJSON`: Generate a new `PrivValidatorJSON` with a random Ed25519 key.
    *   `LoadPrivValidatorJSON(filePath string) *PrivValidatorJSON`: Load a `PrivValidatorJSON` from the specified file path.
    *   `LoadOrGenPrivValidatorJSON(filePath string) *PrivValidatorJSON`: Load an existing `PrivValidatorJSON` or generate and save a new one if it doesn't exist.

*   Define the `PrivValidatorJSON` struct:
    *   Ensure JSON serialization with fields: `id`, `priv_key`, `last_signed_info`.
    *   Implement methods: `Address()`, `PubKey()`, `SignVote()`, `SignProposal()`, `SignHeartbeat()`, `Save()`.

*   Implement signing logic:
    *   `SignVote`: Sign votes, preventing double-signing by tracking height, round, and step.
    *   `SignProposal`: Sign proposals with similar constraints as votes.
    *   Handle timestamp-only differences by restoring the original timestamp and reusing the signature.

*   Develop socket-based client/server:
    *   `NewSocketClient`: Create a client that proxies operations to a remote server.
    *   `NewPrivValidatorSocketServer`: Create a server that listens on a TCP address, handling concurrent requests.
    *   Implement `SocketClientTimeout` to set connection retry timeouts.
    *   Define `ErrDialRetryMax` with the message "Error max client retries".

*   Ensure `LastSignedInfo` is accessible and persists across save/load cycles.

*   Implement `NewTestPrivValidator` to return a `PrivValidatorJSON` for testing, using a `types.TestSigner`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.