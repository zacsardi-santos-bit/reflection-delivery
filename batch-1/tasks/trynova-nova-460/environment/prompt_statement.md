I'm working on a JavaScript engine implemented in Rust, and I'm seeing a large number of typed array conformance tests crash at runtime. After investigating, I believe there are two root causes.

First, the trait used to describe viewable binary types doesn't carry any information about which JavaScript prototype corresponds to each type. This means the engine has no way to look up the right prototype when constructing or operating on a typed array — it crashes instead. Every binary type that typed arrays can be backed by needs to declare which prototype it belongs to.

Second, the clamped unsigned byte typed array variant is completely absent from the system. There's no type representing it, no implementation of the viewable trait for it, and no wiring to its prototype. Since this is one of the standard typed array types, its absence causes crashes in many places that iterate over all typed array kinds.

Could you add prototype information to each binary type used in the typed array system, and also add a proper clamped byte type that uses clamped conversion semantics when writing values to the buffer? After these changes, the engine should be able to properly handle typed array prototype lookups and support the clamped byte array type, resolving a large number of conformance test crashes.
