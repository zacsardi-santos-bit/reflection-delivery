Implement a new XML library package for TypeSpec that provides decorators and utilities to map TypeSpec models to XML. Ensure the package supports XML-specific serialization annotations, namespace handling, and encoding utilities.

*   Implement the @Xml.name decorator:
    *   Set the encoded name of a model or property using the TypeSpec encoding system with media type 'application/xml'.
    *   Ensure resolveEncodedName returns the provided name for that media type.

*   Implement the @Xml.attribute decorator:
    *   Mark a model property as an XML attribute.
    *   Ensure isAttribute(program: Program, property: ModelProperty) returns true if the decorator is applied, false otherwise.

*   Implement the @Xml.unwrapped decorator:
    *   Mark a model property as unwrapped.
    *   Ensure isUnwrapped(program: Program, property: ModelProperty) returns true if the decorator is applied, false otherwise.

*   Implement the @Xml.ns decorator:
    *   Set the XML namespace for a model or property.
    *   Support two modes: (namespaceUrl: string, prefix: string) or a single enum member argument.
    *   Ensure getNs(program: Program, target: Type) returns { namespace: string, prefix: string } or undefined.
    *   Emit diagnostics for invalid usage:
        *   '@typespec/xml/ns-enum-not-declaration' if enum is not marked with @Xml.nsDeclarations.
        *   '@typespec/xml/invalid-ns-declaration-member' if enum member has no string value.
        *   '@typespec/xml/prefix-not-allowed' if prefix is provided with an enum member.
        *   '@typespec/xml/ns-not-uri' if the namespace string is not a valid URI.

*   Implement the @Xml.nsDeclarations decorator:
    *   Mark an enum as a namespace declarations enum for use with @Xml.ns.

*   Implement getXmlEncoding function:
    *   Return default XML encodings for common scalar types:
        *   'TypeSpec.Xml.Encoding.xmlDateTime' for utcDateTime or offsetDateTime.
        *   'TypeSpec.Xml.Encoding.xmlDuration' for duration.
        *   'TypeSpec.Xml.Encoding.xmlDate' for plainDate.
        *   'TypeSpec.Xml.Encoding.xmlTime' for plainTime.
        *   'TypeSpec.Xml.Encoding.xmlBase64Binary' for bytes.
    *   Return explicitly specified encoding if an @encode decorator is applied.

*   Export XmlTestLibrary from packages/xml/src/testing/index.ts:
    *   Ensure it is a valid TypeSpec test library supporting the Xml namespace via autoUsings: ['TypeSpec.Xml'].

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.