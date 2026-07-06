Enable context propagation by default in Beyla to ensure distributed tracing works out of the box. Update the configuration to reflect this change while allowing users the option to disable it if desired.

*   Modify the EBPFTracer configuration struct:
    *   Add a boolean field named `ContextPropagationEnabled`.
    *   Ensure this field controls whether distributed trace context propagation is active.
*   Update the default configuration in `pkg/beyla/config.go`:
    *   Set the `ContextPropagationEnabled` field in the `DefaultConfig` variable's `EBPF` field to `true`.
    *   Ensure the `EBPF` field is of type `config.EBPFTracer`.
*   Ensure that when configuration is loaded without an explicit override for context propagation, the `ContextPropagationEnabled` field is set to `true`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.