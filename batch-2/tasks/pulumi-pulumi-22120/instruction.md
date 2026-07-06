I noticed that when Pulumi serializes resource state to a snapshot, output values that have actually resolved to known values are being stored incorrectly.

*   When SerializePropertyValue encounters an output value whose Known field is false (unknown output), it must serialize it as the computed value placeholder string "04da6b54-80e4-46f7-96ec-b56ff0331ba9", identical to how a Computed value is serialized.

*   When SerializePropertyValue encounters an output value whose Known field is true (known output), it must serialize the inner Element value directly — e.g., a known output wrapping a string "strProp" should serialize to "strProp".

*   When SerializePropertyValue encounters an output value whose Known field is true and Secret field is true (known secret output), it must serialize the inner Element value as an encrypted secret, producing the standard Pulumi secret wire format with the secret signature key "4dabf18193072939515e22adb298388d" and value "1b47061264138c4ac30d75fd1eb44270".

*   Computed values (non-output) must continue to serialize as the placeholder string "04da6b54-80e4-46f7-96ec-b56ff0331ba9" unchanged.

*   When serializing and then deserializing a property value in a round-trip, output values should be reconstructable to their expected inner shape: an unknown output maps to a computed value, a known output maps to its inner element, and a known secret output maps to a secret wrapping its inner element.


*   Interface details: Type: Function
Name: SerializePropertyValue
Location: pkg/resource/stack/deployment.go
Signature: SerializePropertyValue(ctx context.Context, prop resource.PropertyValue, enc config.Encrypter, showSecrets bool) (interface{}, error)
Description: Existing function that serializes a resource property value to a wire-format representation. Must be modified to handle output values differently based on their Known and Secret fields: unknown outputs serialize as the computed value placeholder; known outputs serialize their inner element value directly; known+secret outputs serialize as an encrypted secret containing the inner element value.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.