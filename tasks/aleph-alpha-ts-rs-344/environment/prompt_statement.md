I'm using a Rust library that generates TypeScript type definitions from Rust types. I've run into a couple of bugs with how tagged enums and flattened types are handled.

First, when I annotate a Rust type with a tag attribute to add a discriminant field to the TypeScript output, the tag key comes out unquoted. Instead of the tag key being a quoted string literal, it appears as a bare identifier. This is incorrect and causes issues on the TypeScript side.

Second, I have a fairly common pattern where one internally-tagged enum is used as a flattened field inside a variant of another internally-tagged enum. When I try to generate TypeScript from this, the output is wrong — instead of an intersection type that correctly combines the outer variant's fields with the inner enum's union, something is broken in the generation. I'd expect the variant's own fields to be combined with the inner enum's variants as an intersection type.

Also, when a struct simply wraps an enum via a flattened field, the generated declaration for the struct should be the same as for the enum itself — but I'm not sure if that's working properly either.

Could you fix the TypeScript generation so that tag keys are always quoted as string literals, and that flattening a tagged enum inside another tagged enum variant produces correct intersection types in the output?
