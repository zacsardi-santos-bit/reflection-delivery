Simplify the CPUID processing API in a virtual machine monitor by modifying the VM specification constructor to automatically determine the CPU vendor identifier internally. Update all relevant code to use the new constructor form, and refactor the CPUID transformer infrastructure for improved composability.

*   Implement `VmSpec::new` to:
    *   Accept parameters: `cpu_id: u8`, `cpu_count: u8`, `ht_enabled: bool`.
    *   Return `Result<VmSpec, Error>`.
    *   Internally query the host CPU vendor identifier using `get_vendor_id`.
    *   Propagate any errors from the vendor ID query through the `Result` type.
    *   Ensure successful return of `Ok(VmSpec)` when called with valid parameters (e.g., `cpu_id=0`, `cpu_count=1`, `ht_enabled=false`).
*   Implement `VmSpec` to:
    *   Expose method `cpu_vendor_id(&self) -> &[u8; 12]` to return the vendor identifier.
    *   Be publicly re-exported from the `cpuid` crate as `cpuid::VmSpec`.
*   Refactor `CpuidTransformer` trait to:
    *   Provide a `process_cpuid` method with a default implementation that delegates to `process_entries`.
    *   Provide a `process_entries` method with a default implementation that iterates over all entries and calls `entry_transformer_fn`.
    *   Provide an `entry_transformer_fn` method that implementors override to return the appropriate entry transformer for a given CPUID entry.
*   Update `update_feature_info_entry` in `cpuid/src/transformer/intel.rs` to:
    *   Return `Ok(())`.
    *   Set the TSC deadline timer bit (ECX register, bit index 24) to true.
*   Update `update_perf_mon_entry` in `cpuid/src/transformer/intel.rs` to:
    *   Return `Ok(())`.
    *   Set all four registers (EAX, EBX, ECX, EDX) of the CPUID entry to 0.
*   Modify `filter_cpuid` in `cpuid/src/lib.rs` to:
    *   Accept parameters `(kvm_cpuid: &mut CpuId, vm_spec: &VmSpec)`.
    *   Dispatch to the appropriate transformer based on `vm_spec.cpu_vendor_id()`.
*   Modify `set_cpuid_entries` in `cpuid/src/template/t2.rs` and `cpuid/src/template/c3.rs` to:
    *   Accept parameters `(kvm_cpuid: &mut CpuId, vm_spec: &VmSpec)`.
    *   Return `Result<(), Error>`.
*   Update all existing callers of the old `VmSpec::new` to use the new three-argument form and handle the `Result` with `.expect("Error creating vm_spec")` or equivalent.
*   Add tests:
    *   For `VmSpec::new` in `cpuid/src/transformer/intel.rs` to verify success with parameters `(0, 1, false)`.
    *   For `update_feature_info_entry` to verify success on a zeroed leaf 0x1 entry and ECX bit 24 set to true.
    *   For `update_perf_mon_entry` to verify success on an entry with all registers set to 1 and all registers zeroed afterward.
    *   For `process_cpuid` dispatching in `cpuid/src/transformer/mod.rs` using a mock `CpuidTransformer`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.