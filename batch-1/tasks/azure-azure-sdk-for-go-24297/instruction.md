Implement a new internal tracing package for the Azure Service Bus Go SDK to support distributed tracing for messaging operations. Ensure that trace context is propagated, message metadata is extracted as span attributes, and operations are correctly classified into span categories.

*   Create the tracing package at `sdk/messaging/azservicebus/internal/tracing/` with the package name 'tracing'.
*   Define types and constants:
    *   `MessagingOperationName` and `MessagingOperationType` string types, with constants for each operation name and type as specified in `interface.md`.
    *   Span kind constants (`SpanKindInternal`, `SpanKindProducer`, `SpanKindClient`) as aliases of azcore tracing equivalents.
    *   Attribute key string constants (e.g., `AttrMessageID`, `AttrConversationID`) with exact string values from `interface.md`.
    *   An unexported constant `enqueuedTimeAnnotation` with the value 'x-opt-enqueued-time'.
*   Implement `getMessageAttributes` function:
    *   Return nil for a nil message.
    *   Return an empty slice for messages with no properties, header, or annotations.
    *   Include `AttrMessageID` and `AttrConversationID` when respective properties are set.
    *   Include `AttrEnqueuedTime` as a Unix timestamp from annotations and `AttrDeliveryCount` as `int64(Header.DeliveryCount + 1)`.
    *   Return early if annotations are non-nil but do not contain `enqueuedTimeAnnotation`.
*   Implement `messageCarrierAdapter` function:
    *   Return a `tracing.Carrier` backed by the message's `ApplicationProperties`.
    *   Handle nil messages by wrapping a fresh empty `amqp.Message`.
    *   Ensure `Get` returns an empty string for missing or non-string values.
*   Implement `StartSpan` function:
    *   Return the original context unchanged with a no-op end function when options are nil or `OperationName` is empty.
    *   Include `AttrOperationName` and `AttrOperationType` attributes when creating a span.
    *   Form span names as `<OperationName> <destination>` or `<OperationName>` if the destination is empty.
*   Implement `getOperationType` function to map operation names to types as specified.
*   Implement `getSpanKind` function:
    *   Return `SpanKindProducer` for `CreateOperationType` and `SendOperationType` with `batchCount==0`.
    *   Return `SpanKindClient` for `SendOperationType` with `batchCount>0`, `ReceiveOperationType`, `SettleOperationType`, and session operations.
    *   Return `SpanKindInternal` for all other cases.
*   Define the `Tracer` struct with unexported fields `tracer` and `destination`.
*   Define the `StartSpanOptions` struct with exported fields `Tracer`, `OperationName`, `Message`, `BatchCount`, and `Links`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.