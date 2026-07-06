Update the Sui blockchain codebase to register protocol version 55, enabling enum types for smart contracts on mainnet. Modify the protocol configuration, public API specification, and genesis configuration snapshots to reflect this new version.

*   Update the maximum supported protocol version:
    *   Change the constant `MAX_PROTOCOL_VERSION` in `crates/sui-protocol-config/src/lib.rs` from 54 to 55.
*   Add protocol version 55 configuration:
    *   Insert a new match arm in `crates/sui-protocol-config/src/lib.rs` for version 55:
        *   Signature: `55 => { cfg.move_binary_format_version = Some(7); }`
        *   This enables enum support on mainnet by setting `move_binary_format_version` to 7.
*   Update the OpenRPC specification:
    *   Modify `crates/sui-open-rpc/spec/openrpc.json` to set `maxSupportedProtocolVersion` to "55".
*   Refresh genesis configuration:
    *   Ensure the genesis configuration snapshot defaults to `protocol_version` 55.
    *   Update all on-chain object identifiers derived from the genesis transaction to reflect protocol version 55.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.