I'm stuck on the codecs crate not compiling and it's blocking everything, all the tests fail because of it. The issue is there's a "text" encoding format that a bunch of sinks reference by name but the actual encoder type was never defined. Right now the raw-message encoder is being used as a stand-in for it which is just wrong, I need a real dedicated text serializer added to the encoding format module.

Behavior-wise it should work like the other serializers we already have. For a log event it should pull the message field off the event and write its bytes to the output buffer, and for a metric it should convert it to the human-readable string form and write those bytes out. Pretty simple, just mirror what the existing format types do.

Also the config type needs a constructor, and both the config type and the serializer type need to get wired into the shared encoding enums (the configuration enum and the serializer enum) so the format can be selected by name with a matching variant for each. And they both need to be publicly exported from the codecs library alongside the existing format types so downstream sink code can reference them directly by name.

Without this the whole thing won't build, so that's the priority. Once the text format is a first-class encoder type the sinks can actually migrate onto the new encoding framework properly.
