Implement serialization utilities to handle polymorphic data payloads in distributed process runtime events and messages. Ensure that the serialization and deserialization process fully restores the original data types without requiring pre-registration.

*   Implement `KernelProcessEventSerializer` in `dotnet/src/Experimental/Process.Runtime.Dapr/Serialization/KernelProcessEventSerializer.cs`:
    *   Provide `ToJson(this KernelProcessEvent processEvent) -> string` to serialize `KernelProcessEvent` to JSON with type metadata for `Data`.
    *   Provide `ToKernelProcessEvents(this IEnumerable<string> jsonEvents) -> IList<KernelProcessEvent>` to deserialize JSON strings back to `KernelProcessEvent` objects, restoring types like int, string, Guid, int[], user-defined objects, and `KernelProcessError`.

*   Implement `ProcessEventSerializer` in `dotnet/src/Experimental/Process.Runtime.Dapr/Serialization/ProcessEventSerializer.cs`:
    *   Provide `ToJson(this ProcessEvent processEvent) -> string` to serialize `ProcessEvent` to JSON with type metadata for `Data`.
    *   Provide `ToProcessEvents(this IEnumerable<string> jsonEvents) -> IList<ProcessEvent>` to deserialize JSON strings back to `ProcessEvent` objects, restoring types like int, string, Guid, int[], user-defined objects, and `KernelProcessError`.

*   Implement `ProcessMessageSerializer` in `dotnet/src/Experimental/Process.Runtime.Dapr/Serialization/ProcessMessageSerializer.cs`:
    *   Provide `ToJson(this ProcessMessage processMessage) -> string` to serialize `ProcessMessage` to JSON with type metadata for `Values` dictionary and `TargetEventData`.
    *   Provide `ToProcessMessages(this IEnumerable<string> jsonMessages) -> IList<ProcessMessage>` to deserialize JSON strings back to `ProcessMessage` objects, restoring types for all `Values` entries.

*   Refactor `ProcessEvent` in `dotnet/src/InternalUtilities/process/Runtime/ProcessEvent.cs`:
    *   Define as a record with init-only properties: `Namespace`, `SourceId`, `Data`, `Visibility`, `IsError`, and computed `QualifiedId`.
    *   Remove `DataContract` and `DataMember` attributes.
    *   Add static factory method `Create(KernelProcessEvent, string eventNamespace, bool isError = false)`.

*   Update `KernelProcessEvent` in `dotnet/src/Experimental/Process.Abstractions/KernelProcessEvent.cs`:
    *   Ensure `Id` is a non-nullable string with init setter, default `string.Empty`.
    *   Define `Data` as object? with init setter.

*   Refactor `KernelProcessError` in `dotnet/src/Experimental/Process.Abstractions/KernelProcessError.cs`:
    *   Use init-only properties: `Type`, `Message`, `StackTrace`, `InnerError`.
    *   Remove `DataContract` and `DataMember` attributes.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.