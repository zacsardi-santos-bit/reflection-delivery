I'm working on the codecs library and it currently fails to compile because a text encoding format is referenced but never actually defined. Several sinks use a "text" encoding mode, but the encoder type itself doesn't exist yet — right now the raw-message encoder is being used as a stand-in, which is wrong.

I need to add a proper, dedicated text serializer format to the encoding format module. It should behave like the existing serializers: for log events, it should extract the message field and output its bytes; for metrics, it should produce their human-readable string form. The configuration type needs a constructor, and both the config and the serializer types need to be wired into the shared encoding enums so they can be selected by name. They also need to be publicly exported from the library so downstream code can reference them directly.

Without this, the entire codecs crate won't compile and all tests fail.
