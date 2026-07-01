Extract the gossipsub implementation from the main networking library and create a standalone Rust crate. Ensure that all existing functionality and tests continue to work, and update imports to reflect the new crate structure.

*   Create a new Rust crate named 'gossipsub' at `beacon_node/lighthouse_network/gossipsub/`.
    *   Define the package name as 'gossipsub' in `Cargo.toml`.
    *   Place all source files under `beacon_node/lighthouse_network/gossipsub/src/`.

*   Update the crate root (`lib.rs`) to publicly export:
    *   ValidationError
    *   TopicScoreParams
    *   IdentTopic (type alias for Topic with IdentityHash)
    *   Message
    *   Rpc (as a type alias)
    *   DataTransform, IdentityTransform (from the transform module)
    *   RawMessage (from the types module)

*   Ensure the following modules and their exports:
    *   `subscription_filter.rs`: Export WhitelistSubscriptionFilter.
    *   `transform.rs`: Export DataTransform and IdentityTransform.
    *   `types.rs`: Export RpcOut, RpcReceiver, Rpc, and RawMessage.
    *   `config.rs`: Export Config and ConfigBuilder.

*   Modify internal imports within the gossipsub crate:
    *   Use 'crate::' as the root prefix for all internal module imports.

*   Replace the async runtime dependency:
    *   Use `std::net::Ipv4Addr` instead of any async runtime's Ipv4Addr type.

*   Update the `beacon_node/network` crate:
    *   Declare gossipsub as a direct named dependency in `Cargo.toml`.

*   Ensure all existing gossipsub tests compile and pass:
    *   Tests include behaviour, peer_score, config, mcache, protocol, rpc_proto, subscription_filter, and time_cache.
    *   Use the `crate::` prefix for imports relative to the gossipsub crate root.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.