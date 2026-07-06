I'm running into a data-loss issue with how Pulumi handles map properties that contain certain special keys.

*   The RPC layer that converts protobuf structs to resource property maps must only strip or filter keys starting with '__' at the top level of the property map. Keys starting with '__' that appear inside nested map values must be preserved exactly as-is.

*   When the Go SDK unmarshals a map-typed resource property value, it must preserve keys starting with '__' (e.g. '__default') along with their associated values. Previously these were stripped; after the fix they must be present in the resulting map.

*   A language conformance test named 'l2-map-keys-adversarial' must be registered in the LanguageTests map (in pkg/testing/pulumi-test-language/tests/) and must validate end-to-end handling of adversarial map keys.

*   The language conformance test must use PCL program data at pkg/testing/pulumi-test-language/tests/testdata/l2-map-keys-adversarial/main.pp. The program must define a primitive:index:Resource with a booleanMap property containing the adversarial keys, and invoke primitive:index:invoke with the same map. Both outputs (resourceBooleanMap and invokeBooleanMap) must be exported as stack outputs.

*   The adversarial booleanMap must contain exactly these entries: '__type' mapped to boolean true, '__internal' mapped to boolean false, '__provider' mapped to boolean true, '__version' mapped to boolean false, an empty string key mapped to boolean true, and the key 'Some ${common} "characters" \'that\' need escaping: \\ (backslash), \t (tab), \u001b (escape), \u0007 (bell), \u0000 (null), \U000e0021 (tag space)' mapped to boolean false.

*   The language conformance test must verify that exactly 3 resources exist in the snapshot: the stack resource, the primitive provider, and a primitive:index:Resource.

*   The language conformance test must verify that the primitive:index:Resource's Inputs equal a PropertyMap containing boolean=false, float=2.17, integer=-12, string='adversarial', numberArray=[0,1], and booleanMap equal to the adversarial map defined above.

*   The language conformance test must verify that the primitive:index:Resource's Outputs equal its Inputs.

*   The language conformance test must verify that the stack output named 'resourceBooleanMap' equals the adversarial PropertyMap, and that the stack output named 'invokeBooleanMap' also equals the adversarial PropertyMap.


*   Interface details: NO INTERFACES NEEDED

The required changes are behavioral modifications to existing functions in the RPC marshaling layer and the Go SDK unmarshaling layer. No new exported functions, classes, or types with specific names need to be introduced for the tests to pass.

The new language conformance test files are provided in test.patch itself:
- Test registration: pkg/testing/pulumi-test-language/tests/l2_map_keys_adversarial.go (registers "l2-map-keys-adversarial" in the LanguageTests map)
- Test data: pkg/testing/pulumi-test-language/tests/testdata/l2-map-keys-adversarial/main.pp (PCL program defining adversarial map keys)

The production code changes are in existing functions within:
- sdk/go/common/resource/plugin/rpc.go (internal-key filtering logic)
- sdk/go/pulumi/rpc.go (map value unmarshaling logic)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.