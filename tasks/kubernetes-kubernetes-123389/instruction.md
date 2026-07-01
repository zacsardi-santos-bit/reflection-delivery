Implement the ability to load a textual snapshot of nftables rules into the simulated nftables environment for unit testing. This will allow tests to initialize the environment with static rule data, enabling focused, standalone packet routing tests. Fix the existing IPv6 support bug to ensure correct packet routing tests for both IPv4 and IPv6.

*   Implement the `ParseDump` method on the `Fake` type in `vendor/sigs.k8s.io/knftables/fake.go`:
    *   Accept a string in the format produced by `Fake.Dump()`.
    *   Load all nftables objects into the `Fake` instance's state.
    *   Return a non-nil error if parsing fails, or if the table family or name does not match the `Fake`'s configuration.
    *   Ensure `Fake.Dump()` produces output equivalent to the input after a successful call.

*   Update the Object interface in `vendor/sigs.k8s.io/knftables/types.go`:
    *   Add the `parse(line string) error` method to the interface.
    *   Implement `parse(line string) error` for each concrete type in `vendor/sigs.k8s.io/knftables/objects.go`:
        *   `Table`: Fill fields from "add table" command.
        *   `Chain`: Fill fields from "add chain" command.
        *   `Rule`: Fill fields from "add rule" command.
        *   `Set`: Fill fields from "add set" command.
        *   `Map`: Fill fields from "add map" command.
        *   `Element`: Determine and fill fields for map or set elements.

*   Ensure IPv4 packet flow simulation works through loaded rules:
    *   Handle scenarios like no matching service, single/multiple endpoint routing, masquerade marking, DROP/REJECT verdicts, firewall source-range filtering, and NodePort routing.

*   Ensure IPv6 packet flow simulation works through loaded rules:
    *   Handle scenarios like pod-to-cluster-IP routing, external-to-NodePort traffic, and node-to-NodePort traffic using IPv6 addresses in bracket notation.

*   Update dependency version for `sigs.k8s.io/knftables` to v0.0.16 in `go.mod`, `go.sum`, `go.work.sum`, and `vendor/modules.txt` to incorporate the `ParseDump` implementation and IPv6 fix.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.