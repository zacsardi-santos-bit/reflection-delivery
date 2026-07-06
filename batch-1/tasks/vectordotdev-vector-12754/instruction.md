Implement a dedicated text serializer format in the codecs library to resolve compilation issues and support a "text" encoding mode. Ensure the new serializer extracts log messages and converts metrics to human-readable strings, integrating seamlessly with existing encoding enums.

*   Create a new module `text` under `lib/codecs/src/encoding/format/`.
    *   Declare and re-export this module in `lib/codecs/src/encoding/format/mod.rs` using `mod text;` and `pub use text::{TextSerializer, TextSerializerConfig};`.
*   Define a unit struct `TextSerializerConfig` in `lib/codecs/src/encoding/format/text.rs`.
    *   Derive Debug, Clone, Default, Deserialize, and Serialize for `TextSerializerConfig`.
    *   Implement a `const fn new() -> Self` constructor.
    *   Implement a `const fn build(&self) -> TextSerializer` method.
    *   Implement an `input_type(&self) -> DataType` method returning `DataType::Log | DataType::Metric`.
    *   Implement a `schema_requirement(&self) -> schema::Requirement` method.
*   Define a unit struct `TextSerializer` in `lib/codecs/src/encoding/format/text.rs`.
    *   Derive Debug and Clone for `TextSerializer`.
    *   Implement `tokio_util::codec::Encoder<Event>` for `TextSerializer` with `type Error = vector_core::Error`.
    *   Implement a `const fn new() -> Self` constructor.
    *   Implement an `encode(&mut self, event: Event, buffer: &mut BytesMut) -> Result<(), Self::Error>` method.
        *   For `Event::Log`, extract the message key using `get_by_meaning` or `get`, convert it to bytes, and write to the buffer.
        *   For `Event::Metric`, convert the metric to a string using its Display implementation and write to the buffer.
        *   For `Event::Trace`, perform a no-op and return `Ok(())`.
*   Update `lib/codecs/src/encoding/mod.rs`:
    *   Publicly re-export `TextSerializer` and `TextSerializerConfig`.
    *   Add a `Text` variant to the `SerializerConfig` enum with `From<TextSerializerConfig>`, `build()`, `input_type()`, and `schema_requirement()` implementations.
    *   Add a `Text(TextSerializer)` variant to the `Serializer` enum with a `From<TextSerializer>` implementation and an `encode` dispatch arm.
*   Update `lib/codecs/src/lib.rs` to re-export `TextSerializer` and `TextSerializerConfig`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.