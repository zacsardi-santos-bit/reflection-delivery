Implement additional convenience methods in the gRPC Swift code generator to simplify client protocol types. These methods should allow callers to pass messages directly without manually constructing request wrapper objects, reducing verbosity and aligning with Swift's ergonomic API design.

*   Update `LiteralDescription` enum in `Sources/GRPCCodeGen/Internal/StructuredSwiftRepresentation.swift`:
    *   Add a `dictionary([KeyValue])` case.
    *   Define `KeyValue` as a `Codable, Equatable` struct with `var key: Expression` and `var value: Expression`.

*   Modify `renderLiteral` method in `Sources/GRPCCodeGen/Internal/Renderer/TextBasedRenderer.swift`:
    *   Handle the `.dictionary` case.
    *   Render an empty dictionary as `[:]`.
    *   Render non-empty dictionaries in a multi-line format with indented entries.

*   Update `translate(from:)` method in `Sources/GRPCCodeGen/Internal/Translator/ClientCodeTranslator.swift`:
    *   Emit a platform-availability-guarded extension on `<NamespacedName>.ClientProtocol` for each service.
    *   Include convenience wrapper methods for each RPC method, even for services with no methods.

*   Implement convenience methods for different RPC patterns:
    *   **Unary (non-streaming input, non-streaming output)**:
        *   Accept `_ message: InputType`, `metadata: GRPCCore.Metadata = [:]`, `options: GRPCCore.CallOptions = .defaults`, and `onResponse handleResponse` with a default of `{ try $0.message }`.
        *   Internally build `GRPCCore.ClientRequest.Single<InputType>(message: message, metadata: metadata)`.

    *   **Server streaming (non-streaming input, streaming output)**:
        *   Accept `_ message: InputType`, `metadata`, `options`, and a required `onResponse handleResponse` closure.
        *   Internally build `GRPCCore.ClientRequest.Single`.

    *   **Client streaming (streaming input, non-streaming output)**:
        *   Accept `metadata`, `options`, `requestProducer: @Sendable @escaping (GRPCCore.RPCWriter<InputType>) async throws -> Void`, and `onResponse handleResponse` with a default of `{ try $0.message }`.
        *   Internally build `GRPCCore.ClientRequest.Stream<InputType>(metadata: metadata, producer: requestProducer)`.

    *   **Bidirectional streaming (streaming input, streaming output)**:
        *   Accept `metadata`, `options`, `requestProducer`, and a required `onResponse handleResponse` closure.
        *   Internally build `GRPCCore.ClientRequest.Stream`.

*   Ensure all convenience methods:
    *   Are generic over `Result: Sendable`.
    *   Declared `async throws -> Result`.
    *   Use the same access modifier as the rest of the generated code.

*   Regenerate all pre-generated `.grpc.swift` files to include the new convenience extensions:
    *   Update files in `Sources/InteroperabilityTests/Generated/`, `Tests/GRPCHTTP2TransportTests/Generated/`, and any other relevant locations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.