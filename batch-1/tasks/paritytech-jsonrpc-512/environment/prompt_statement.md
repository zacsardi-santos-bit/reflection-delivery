I'm working with the `jsonrpc-derive` crate's pubsub macro system and I've run into a limitation: each subscription can only have a single subscribe method. I'd like to define multiple subscribe entry points for the same subscription topic — each with a different method name and potentially different parameters — while sharing the same unsubscribe handler.

Right now, if I try to add a second subscribe method for the same subscription, the macro fails at compile time with an error saying the subscribe method is already defined. I'd like the macro to allow multiple subscribe methods per subscription instead.

There's also an important safety check needed: if multiple subscribe methods for the same subscription use different subscriber types (conflicting generics), the macro should detect this and emit a helpful compile error pointing to the mismatch.
