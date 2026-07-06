Implement a consistent use of the `OrigDstAddr` type throughout the inbound proxy policy system to simplify the codebase. Update the policy lookup interface to be synchronous and modify the test proxy builder to allow disabling protocol detection on specific inbound ports without a policy controller. Ensure the proxy returns a "bad gateway" response when an inbound server is unavailable.

*   Update `AllowPolicy::for_test` to accept `OrigDstAddr` as the first argument.
    *   Return a tuple of `(AllowPolicy, watch::Sender<ServerPolicy>)`.
    *   Located in `linkerd/app/inbound/src/policy.rs`.
*   Modify the `GetPolicy` trait:
    *   Make `get_policy` synchronous, accepting `OrigDstAddr` and returning `AllowPolicy`.
    *   Remove dependency on a tower `Service` blanket implementation.
*   Change `AllowPolicy::dst_addr` to return `OrigDstAddr`.
*   Update `ServerPermit` and `HttpRoutePermit` structs:
    *   Change the `dst` field to type `OrigDstAddr`.
*   Adjust `check_authorized` function:
    *   Accept `OrigDstAddr` as the second parameter.
    *   Ensure `ServerPermit.dst` is set to the provided `OrigDstAddr`.
    *   Located in `linkerd/app/inbound/src/policy/tcp.rs`.
*   Modify `ConnectionMeta` struct in `linkerd/app/inbound/src/policy/http.rs`:
    *   Change `dst` field to type `OrigDstAddr`.
*   Define a `DefaultPolicy` enum:
    *   Variants: `Allow(ServerPolicy)` and `Deny`.
    *   Export from `linkerd/app/inbound/src/policy.rs` and re-export from `linkerd/app/inbound/src/lib.rs`.
*   Update `Store<MockSvc>` type in `linkerd/app/inbound/src/policy/tcp/tests.rs`:
    *   Implement `for_test` method accepting `default` and `ports`.
    *   Ensure `MockSvc` implements `tonic::client::GrpcService<tonic::body::BoxBody>`.
*   Revise `Store::spawn_fixed` method:
    *   Accept `DefaultPolicy`, idle timeout, and port iterator.
    *   Remove discovery service type parameter.
*   Delete `accept/tests.rs` file and remove its module declaration from `accept.rs`.
*   Enhance the integration test proxy builder:
    *   Add `disable_inbound_ports_protocol_detection` method to `Proxy` struct.
    *   Configure proxy to skip protocol detection on specified ports.
*   Ensure proxy returns HTTP 502 Bad Gateway response when an inbound service connection times out.
*   Make policy controller setup optional in the `Proxy` struct of the integration test harness.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.