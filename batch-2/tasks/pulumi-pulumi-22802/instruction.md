I'm working with the Pulumi Configuration Language runtime and I've noticed that the built-in string length function gives wrong results for non-ASCII strings.

*   The PCL language test runner must find a project directory at `sdk/pcl/cmd/pulumi-language-pcl/testdata/projects/l1-builtin-string/` containing a `Pulumi.yaml` file (with name `l1-builtin-string` and runtime `pcl`) and a `main.pp` PCL program identical to the one in the shared test data.

*   The PCL program for this test must declare a string config input named `aString` and export four outputs: `lengthResult` (the length of the string), `splitResult` (the string split by `-`), `joinResult` (the split parts joined with `|`), and `interpolateResult` (the string `prefix-` prepended to the input via interpolation).

*   The `length()` builtin function in the PCL runtime must return the number of Unicode grapheme clusters in a string input (not the number of bytes or Unicode code points). For example, an emoji with a variation selector counts as one grapheme cluster, and a ZWJ family sequence counts as one grapheme cluster regardless of how many code points compose it.

*   The `length()` result must be returned as a `float64` numeric value in the stack outputs.

*   The `split(delimiter, string)` builtin must split the input string on the given delimiter and return an array of string values. For example, splitting `foo-bar-baz` on `-` must produce the array `["foo", "bar", "baz"]`.

*   The `join(delimiter, list)` builtin must concatenate the elements of a string list with the given delimiter and return a single string. For example, joining `["foo", "bar", "baz"]` with `|` must produce `"foo|bar|baz"`.

*   String interpolation of the form `"prefix-${aString}"` must produce a string with the literal prefix prepended to the value of `aString`.

*   The `l1-builtin-string` test must be registered in the `LanguageTests` map with `RunsShareSource: true` and multiple test runs covering ASCII strings, multi-byte Latin strings, emoji with variation selectors, and ZWJ family emoji sequences.

*   The Go language test must register `l1-builtin-string` in the `expectedFailures` map with the reason that the Go code generator cannot yet convert the split result type to a string array, so the test is excluded from Go codegen validation.


*   Interface details: Type: File
Name: sdk/pcl/cmd/pulumi-language-pcl/testdata/projects/l1-builtin-string/Pulumi.yaml
Location: sdk/pcl/cmd/pulumi-language-pcl/testdata/projects/l1-builtin-string/Pulumi.yaml
Description: Project manifest for the PCL language test runner. Must contain `name: l1-builtin-string` and `runtime: pcl`.

Type: File
Name: sdk/pcl/cmd/pulumi-language-pcl/testdata/projects/l1-builtin-string/main.pp
Location: sdk/pcl/cmd/pulumi-language-pcl/testdata/projects/l1-builtin-string/main.pp
Description: PCL program for the l1-builtin-string test. Must be identical to the shared test data program at pkg/testing/pulumi-test-language/tests/testdata/l1-builtin-string/main.pp. Declares a config input `aString` of type `string` and exports four outputs: `lengthResult` using `length(aString)`, `splitResult` using `split("-", aString)`, `joinResult` using `join("|", split("-", aString))`, and `interpolateResult` using `"prefix-${aString}"`.

Type: Function (modified)
Name: length (builtin)
Location: sdk/pcl/runtime/builtinFunctions.go
Signature: registered as "length" key in the map returned by builtinFunctions()
Description: The `length` builtin function must be replaced with a version that, when given a string argument, counts Unicode grapheme clusters using the equivalent of `uniseg.GraphemeClusterCount`. For non-string collection types, the existing collection-length behavior must be preserved. The return type is `cty.Number` (a float64-compatible value).

Type: Map entry (modified)
Name: expectedFailures["l1-builtin-string"]
Location: sdk/go/pulumi-language-go/language_test.go
Description: The string `"l1-builtin-string"` must be added as a key in the `expectedFailures` map with the value `"cannot convert strings.Split(aString, \"-\") (value of type []string) to type pulumi.StringArray"` (with a `//nolint:lll` comment). This marks the test as an expected failure for the Go language code generator.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.