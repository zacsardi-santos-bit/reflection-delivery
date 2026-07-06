Implement path-based routing for the built-in HTTP server in wasmCloud to allow a single HTTP listener to route requests to different components based on URL paths. Add a test utility to stop a provider and confirm its shutdown.

*   Update the wasmcloud-host crate:
    *   Convert `crates/host/src/wasmbus/providers/http_server.rs` into a directory module:
        *   Create `mod.rs` for module root with factory logic and shared `listen()` helper.
        *   Move address-based routing logic to `address.rs`.
        *   Implement path-based routing logic in `path.rs`.
    *   In `path.rs`, define a `Router` struct:
        *   Derive `Default`.
        *   Include `paths: HashMap<Arc<str>, Arc<str>>` for URL path to component ID mapping.
        *   Include `components: HashMap<(Arc<str>, Arc<str>), Arc<str>>` for (component_id, link_name) to URL path mapping.
    *   Define a `Provider` struct in `path.rs`:
        *   Include `handle: JoinSet<()>` for the server task handle.
        *   Include `path_router: Arc<RwLock<Router>>` for shared routing state.
    *   Implement `put_link` method for `Provider`:
        *   Signature: `async fn put_link(&self, target_id: &str, link_name: &str, config: &HashMap<String, String>) -> anyhow::Result<()>`.
        *   Register a URL path from `config["path"]` for the component and link name.
        *   Insert into both `Router.paths` and `Router.components`.
        *   Return an error if the path is already registered or if the (component_id, link_name) pair has a registered path.
    *   Implement `delete_link` method for `Provider`:
        *   Signature: `async fn delete_link(&self, source_id: &str, target_id: &str, link_name: &str) -> anyhow::Result<()>`.
        *   Deregister the path for the given (target_id, link_name) pair.
        *   Remove from `Router.components` and the corresponding path from `Router.paths`.
    *   Ensure the HTTP server returns 404 for unregistered paths and dispatches registered paths correctly.
    *   Ensure requests to a removed path return 404, and re-added paths return 200.
    *   Ensure stopping the provider results in all requests failing outright.

*   Add a test utility in `crates/test-util/src/provider.rs`:
    *   Define `StopProviderArgs` struct:
        *   Fields: `client: &'a wasmcloud_control_interface::Client`, `host_id: &'a str`, `provider_id: &'a str`.
    *   Implement `assert_stop_provider` function:
        *   Signature: `async fn assert_stop_provider(StopProviderArgs { client, host_id, provider_id }: StopProviderArgs<'_>) -> Result<()>`.
        *   Send stop command and poll until the provider stops, returning an error if it doesn't stop.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.