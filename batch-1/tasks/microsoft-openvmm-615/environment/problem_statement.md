## Description

The channel types in the mesh communication library currently require message types to implement thread-safety. This restriction is unnecessarily strict: the channels transfer messages via serialization rather than by actually moving data across threads. Because of this, developers cannot use channels with types that contain inherently non-thread-safe data (such as raw pointers), even when those types support serialization.

## Expected Behavior

- Channels should be usable with any message type that supports serialization, regardless of whether the type implements thread-safety
- Converting a receiver to a transport port and back should work for non-thread-safe message types, as long as those types are serializable
- Sending and receiving a message of a non-thread-safe type through a channel should produce the correct value at the receiver

## Why This Matters

Many useful types are deliberately not thread-safe (they contain raw pointers or other non-thread-safe data), yet they are fully serializable. The current design blocks using the mesh channel API with these types, even though the channel would safely serialize and deserialize values rather than sharing memory across threads. Removing this artificial restriction makes the channel types more flexible and accurately reflects how they actually work.
