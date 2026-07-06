I'm working on the RDL interface definition language in windows-rs and want to add first-class property and event shorthands inside interface bodies.

*   The RDL interface definition language must support a property shorthand syntax 'Name: Type;' within interface bodies. Without any attribute, this declares a read-write property and generates both a getter and a setter method with the SpecialName flag.

*   A property declaration preceded by a '#[get]' attribute must generate only a getter method (read-only property). A property declaration preceded by a '#[set]' attribute must generate only a setter method (write-only property).

*   If a property has both '#[get]' and '#[set]' attributes simultaneously, parsing must produce the exact error: "error: property cannot have both `#[get]` and `#[set]` attributes"

*   If a property has any attribute other than '#[get]' or '#[set]', parsing must produce the exact error: "error: only `#[get]` and `#[set]` attributes are supported on properties"

*   The RDL interface definition language must support an event shorthand syntax 'event Name: HandlerType;' within WinRT interface bodies. This generates add_Name and remove_Name method pairs with the SpecialName flag.

*   The RDL writer must convert getter/setter SpecialName method pairs back to the 'Name: Type;' property shorthand when serializing an interface. Getter-only methods must serialize as '#[get] Name: Type;' and setter-only methods as '#[set] Name: Type;'.

*   The RDL writer must convert add_/remove_ SpecialName method pairs back to the 'event Name: HandlerType;' shorthand when serializing WinRT interfaces.

*   The InterfaceMember enum in crates/libs/rdl/src/reader/interface.rs must have variants Method, Property, and Event. The Interface struct must store members as Vec<InterfaceMember> (field name: members).

*   The Property struct in crates/libs/rdl/src/reader/interface.rs must have fields: attrs (Vec<syn::Attribute>), name (syn::Ident), and ty (syn::Type). The Event struct must have fields: name (syn::Ident) and handler_ty (syn::Type).

*   The encode_simple_params method in crates/libs/rdl/src/reader/param.rs must accept a slice of (name, type) pairs and emit Param records with direction flags inferred from the type (mutable references/pointers → Out, otherwise → In).

*   The write_members function in crates/libs/rdl/src/writer/interface.rs must consume SpecialName method pairs to emit property/event shorthands and fall back to the regular method representation for any methods not converted.


*   Interface details: Type: Struct
Name: Property
Location: crates/libs/rdl/src/reader/interface.rs
Description: Represents a property shorthand declaration inside an interface body. Parsed from the syntax `[#[get] | #[set]] Name: Type;`. Without any attribute, both getter and setter are generated; with `#[get]`, only a getter is generated; with `#[set]`, only a setter is generated.
Fields:
  attrs: Vec<syn::Attribute>
  name: syn::Ident
  ty: syn::Type

Type: Struct
Name: Event
Location: crates/libs/rdl/src/reader/interface.rs
Description: Represents an event shorthand declaration inside a WinRT interface body. Parsed from the syntax `event Name: HandlerType;`. Generates add_Name and remove_Name method pairs with the SpecialName flag.
Fields:
  name: syn::Ident
  handler_ty: syn::Type

Type: Enum
Name: InterfaceMember
Location: crates/libs/rdl/src/reader/interface.rs
Description: Discriminated union of all member kinds that can appear inside an interface body.
Variants:
  Method(Method)
  Property(Property)
  Event(Event)

Type: Struct (modified field)
Name: Interface
Location: crates/libs/rdl/src/reader/interface.rs
Description: The Interface struct's field previously named `methods: Vec<Method>` must be renamed to `members: Vec<InterfaceMember>` to accommodate the new member kinds.

Type: Function
Name: encode_simple_params
Location: crates/libs/rdl/src/reader/param.rs
Signature: encode_simple_params(&mut self, params: &[(&str, &metadata::Type)]) -> Result<(), Error>
Description: Encodes a sequence of synthesized parameters with no custom attributes. Direction flags are inferred from the type: mutable references or mutable pointers produce Out, everything else produces In.

Type: Function
Name: write_members
Location: crates/libs/rdl/src/writer/interface.rs
Signature: write_members(namespace: &str, item: &metadata::reader::TypeDef, generics: &[metadata::Type]) -> Result<Vec<TokenStream>, Error>
Description: Emits all interface members as token streams, converting SpecialName getter/setter pairs to property shorthand syntax, SpecialName add/remove pairs to event shorthand syntax, and falling back to the regular method representation for anything else.

Type: Function
Name: find_unconsumed
Location: crates/libs/rdl/src/writer/interface.rs
Signature: find_unconsumed(methods: &[metadata::reader::MethodDef], consumed: &[bool], from: usize, name: &str) -> Option<usize>
Description: Finds the index of an unconsumed method with the given name, searching forward from `from` (wrapping around). Used by write_members to locate paired get_/put_ or add_/remove_ methods.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.