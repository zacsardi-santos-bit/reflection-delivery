I'm working in this mesh channel library where messages get passed between components by serializing them, not by actually moving data across threads. Problem is the channel types, both the sending and receiving sides, currently insist that any message type be thread-safe (basically a `Send`-style bound), and that requirement just doesn't hold up for channels that serialize on send and deserialize on receive. It's an artificial restriction.

I've got some types that hold inherently non-thread-safe data, think raw pointers and similar, but they're fully serializable, and right now I can't use them with the channel API at all even though the channel never really shares memory across threads, it just encodes the value at send time and decodes it at receive time.

So what I want is for the channel sender and receiver to work with any serializable message type regardless of whether it's thread-safe. Converting a receiver into a transport port and back again should work for these non-thread-safe types too, as long as they serialize. And if I send a message of a non-thread-safe but serializable type through a channel and receive it on the other end, I should get the correct value back out.

Basically drop the thread-safety bound everywhere it shows up in the channel implementation, since it's meaningless given how encode/decode actually works.
