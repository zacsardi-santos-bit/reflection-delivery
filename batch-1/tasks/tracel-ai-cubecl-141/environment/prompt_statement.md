I'm trying to define a kernel launch parameter struct that groups multiple array inputs together, but I can't get it to compile. As soon as I add any array-typed fields to a struct and annotate it with the launch parameter derive attribute, I get a compilation error saying a certain method doesn't exist on the array type. It works fine on an empty struct or a unit struct, but breaks the moment I add real fields.

I'd like to be able to define structs like one with two array fields (for left-hand side and right-hand side data) and use the derive attribute on them — including when those arrays are parameterized by a generic float type. The array type simply doesn't seem to implement the trait that the derive macro's generated code requires.

Could someone help fix the array type so it properly implements the trait that the launch parameter macro needs, allowing structs with array fields to be annotated with the launch parameter derive attribute?
