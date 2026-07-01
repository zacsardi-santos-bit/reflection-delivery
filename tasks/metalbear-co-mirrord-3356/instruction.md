Implement a feature to exclude the mirrord agent's communication port from being intercepted by a service mesh sidecar proxy when running as an ephemeral container in a Kubernetes pod. Create a new IPTables chain to manage these exclusions and integrate it into the existing redirect chain setup.

Requirements:

*   Define a public constant:
    *   `IPTABLE_EXCLUDE_FROM_MESH` with value `"MIRRORD_EXCLUDE_FROM_MESH"` in `mirrord-agent-iptables` crate root.

*   Create a new module `exclusion` under `mirrord/agent/iptables/src/mesh/`:
    *   Declare it as `pub mod exclusion` in `mesh.rs`.
    *   Export `MeshExclusion` and `WithMeshExclusion` structs.

*   Implement `MeshExclusion` struct:
    *   `create(ipt: Arc<IPT>, chain: &str)` calls `IPTableChain::create`.
    *   `load(ipt: Arc<IPT>, chain: &str)` calls `IPTableChain::load`.
    *   `mount_entrypoint()` inserts `-j {chain_name}` into `PREROUTING` at position 1.
    *   `unmount_entrypoint()` removes the rule from `PREROUTING`.
    *   `add_exclusion(port: u16)` adds `-p tcp --dport {port} -j ACCEPT` to the chain.
    *   `remove_exclusion(port: u16)` removes the rule from the chain.

*   Implement `WithMeshExclusion` struct:
    *   `create(ipt, inner)` constructs using `IPTABLE_EXCLUDE_FROM_MESH` as chain name.
    *   `load(ipt, inner)` uses `IPTABLE_EXCLUDE_FROM_MESH` as chain name.
    *   Implement `Redirect` trait:
        *   `mount_entrypoint` calls `inner.mount_entrypoint()` then `exclusion.mount_entrypoint()`.
        *   `unmount_entrypoint` calls `inner.unmount_entrypoint()` then `exclusion.unmount_entrypoint()`.
        *   Delegate `add_redirect` and `remove_redirect` to the inner redirect.

*   Update `SafeIpTables`:
    *   `create` accepts a new 5th boolean parameter `with_mesh_exclusion`.
        *   When `true`, wrap the redirect in `WithMeshExclusion::create(ipt, redirect)`.
    *   `load` accepts a new 3rd boolean parameter `with_mesh_exclusion`.
        *   When `true`, wrap the redirect in `WithMeshExclusion::load(ipt, redirect)`.
    *   Add `exclusion()` method returning `Option<&MeshExclusion<IPT>>`.
    *   Include `IPTABLE_EXCLUDE_FROM_MESH` in `list_mirrord_rules` filter.

*   Update `IPTableChain` methods:
    *   `add_rule` and `remove_rule` to accept generic `R: AsRef<str>` parameters.

*   Update call sites of `SafeIpTables::create` to pass `false` for `with_mesh_exclusion`.

*   Add `Redirects` enum variant `WithMeshExclusion(WithMeshExclusion<IPT, Redirects<IPT>>)`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.