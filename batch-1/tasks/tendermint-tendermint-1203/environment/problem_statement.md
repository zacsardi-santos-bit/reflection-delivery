## Description

The current private validator in Tendermint is monolithic: it bundles key management, signing-state tracking, and persistence all in one place, and it runs only as part of the main node process. This makes it hard to extend or reuse for alternative signing backends (e.g., hardware security modules, remote signers, or encrypted key stores).

We need to refactor the private validator into a modular design and add support for running the signing logic in a separate process that communicates with the main node over a network socket.

## Expected Behavior

- A new subpackage is introduced that contains the refactored validator logic.
- The JSON-based validator (backed by an unencrypted key file) must be loadable, saveable, and generatable via dedicated functions.
- The validator must track the last signed height/round/step to prevent double-signing, rejecting votes or proposals that would regress or conflict with already-signed data.
- When a vote or proposal is re-signed at the same height/round/step and only the timestamp differs, the validator must silently restore the original timestamp and reuse the original signature rather than returning an error.
- A socket-based client/server pair must allow a node to delegate all signing operations to a remote process, proxying address retrieval, public key retrieval, vote signing, proposal signing, and heartbeat signing over a TCP connection.
- If the socket client cannot connect after exhausting all retry attempts, it must surface a clear "max retries" error.
- The time format constant used for canonical sign bytes must be made accessible to the new subpackage.

## Why This Matters

This separation improves operational security: node operators can run their signing keys in a hardened, isolated process (or even on dedicated hardware) rather than exposing them inside the main Tendermint binary. It also makes the validator interface cleaner and easier to implement for custom backends.
