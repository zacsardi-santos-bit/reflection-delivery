I'm working with the new union type declaration feature in the C# compiler, and I've noticed that the compiler currently accepts union declarations that contain members which should be forbidden.

*   The compiler must define three new error codes in the ErrorCode enumeration: ERR_InstanceFieldInUnion with value 9404, ERR_InstanceCtorWithOneParameterInUnion with value 9405, and ERR_UnionConstructorCallsDefaultConstructor with value 9406.

*   All three new error codes must be registered in the ErrorFacts class as non-build-only diagnostics (IsBuildOnlyDiagnostic must return false for each).

*   The compiler must emit ERR_InstanceFieldInUnion (CS9404) with message 'Instance fields, auto-properties or field-like events are not permitted in a \'union\' declaration.' when a union declaration contains: any non-static instance field, any auto-property (including get-only, set-only, get-set), any property that uses the 'field' keyword, any explicit interface implementation of an auto-property, or any field-like event (declared without explicit accessor bodies).

*   The compiler must NOT emit ERR_InstanceFieldInUnion for: static fields, static auto-properties, properties with explicit non-field-backed getter/setter bodies (e.g., 'get => 1; set {}'), events with explicit accessor bodies (e.g., 'add {} remove {}'), or static events.

*   The compiler must emit ERR_InstanceCtorWithOneParameterInUnion (CS9405) with message 'Explicitly declared public constructors with a single parameter are not permitted in a \'union\' declaration.' when a union declaration contains an explicitly declared instance constructor with exactly one parameter, regardless of the parameter modifier (value, ref, in, ref readonly, or out).

*   The compiler must NOT emit ERR_InstanceCtorWithOneParameterInUnion for: constructors with two or more parameters, or constructors with zero parameters.

*   The compiler must emit ERR_UnionConstructorCallsDefaultConstructor (CS9406) with message 'A constructor declared in a \'union\' declaration must have a \'this\' initializer that calls a synthesized constructor or an explicitly declared constructor.' when a union declaration contains an instance constructor that either: (a) has no 'this' initializer at all, or (b) has a 'this()' initializer that calls the default parameterless constructor.

*   The compiler must NOT emit ERR_UnionConstructorCallsDefaultConstructor for: static constructors, or instance constructors that have a 'this(args...)' initializer calling another constructor with arguments.

*   For ERR_UnionConstructorCallsDefaultConstructor, when the constructor lacks a 'this' initializer entirely, the error must be reported at the constructor's identifier location; when the constructor has a 'this()' initializer, the error must be reported at the 'this' keyword location of the initializer.

*   The error message resources must include entries for all three new error codes: ERR_InstanceFieldInUnion, ERR_InstanceCtorWithOneParameterInUnion, and ERR_UnionConstructorCallsDefaultConstructor with the exact message texts as specified.


*   Interface details: Type: Enum Value
Name: ERR_InstanceFieldInUnion
Location: src/Compilers/CSharp/Portable/Errors/ErrorCode.cs
Signature: ERR_InstanceFieldInUnion = 9404
Description: Error code emitted when an instance field, auto-property, field-backed property, field-like event, or explicit interface implementation of an auto-property is declared in a union type. The corresponding error message resource must be: "Instance fields, auto-properties or field-like events are not permitted in a 'union' declaration."

Type: Enum Value
Name: ERR_InstanceCtorWithOneParameterInUnion
Location: src/Compilers/CSharp/Portable/Errors/ErrorCode.cs
Signature: ERR_InstanceCtorWithOneParameterInUnion = 9405
Description: Error code emitted when an explicitly declared constructor with exactly one parameter appears in a union declaration. The corresponding error message resource must be: "Explicitly declared public constructors with a single parameter are not permitted in a 'union' declaration."

Type: Enum Value
Name: ERR_UnionConstructorCallsDefaultConstructor
Location: src/Compilers/CSharp/Portable/Errors/ErrorCode.cs
Signature: ERR_UnionConstructorCallsDefaultConstructor = 9406
Description: Error code emitted when a constructor in a union declaration either has no this-initializer or has a this() initializer that calls the default parameterless constructor. The corresponding error message resource must be: "A constructor declared in a 'union' declaration must have a 'this' initializer that calls a synthesized constructor or an explicitly declared constructor."

Type: Resource Entry
Name: ERR_InstanceFieldInUnion
Location: src/Compilers/CSharp/Portable/CSharpResources.resx
Description: Resource string with exact value: "Instance fields, auto-properties or field-like events are not permitted in a 'union' declaration."

Type: Resource Entry
Name: ERR_InstanceCtorWithOneParameterInUnion
Location: src/Compilers/CSharp/Portable/CSharpResources.resx
Description: Resource string with exact value: "Explicitly declared public constructors with a single parameter are not permitted in a 'union' declaration."

Type: Resource Entry
Name: ERR_UnionConstructorCallsDefaultConstructor
Location: src/Compilers/CSharp/Portable/CSharpResources.resx
Description: Resource string with exact value: "A constructor declared in a 'union' declaration must have a 'this' initializer that calls a synthesized constructor or an explicitly declared constructor."

Type: Method (update required)
Name: IsBuildOnlyDiagnostic
Location: src/Compilers/CSharp/Portable/Errors/ErrorFacts.cs
Description: Must return false for ERR_InstanceFieldInUnion, ERR_InstanceCtorWithOneParameterInUnion, and ERR_UnionConstructorCallsDefaultConstructor — all three new error codes must be included in the non-build-only diagnostic list.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.