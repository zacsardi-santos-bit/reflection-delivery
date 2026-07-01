I'm running into a compilation error when I try to use the serialization transformation macro on a struct field that also has a custom schema type annotation. The two attributes conflict — the macro seems to generate its own schema annotation without checking whether one already exists on the field.

What I want to do is have a field that gets serialized in one format (e.g., as a string) for transport, but is declared as a different logical type (e.g., an integer) in the generated JSON schema. Right now, combining these two annotations causes the whole thing to fail to compile.

I also want conditional schema annotations to work properly. If I gate a schema annotation behind a condition that is always false at compile time, the macro should ignore it and generate its own schema. If the annotation is behind a condition that is always true, it should behave the same as an unconditional annotation and the macro should defer to my specified type.

Could you fix the macro so it detects existing schema type annotations on fields and respects them instead of always injecting a potentially conflicting one?
