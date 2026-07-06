I'm trying to use dynamic, runtime-computed values for the style attribute on HTML elements in my templates, but the template engine currently rejects any expression in the style attribute at parse time.

*   Must implement a function SanitizeStyleAttributeValues in the runtime package (runtime/styleattribute.go) with signature SanitizeStyleAttributeValues(values ...any) (string, error) that converts style attribute values of supported types into a sanitized CSS string.

*   Must export a string variable named TemplUnsupportedStyleAttributeValue from the runtime package with the exact value "zTemplUnsupportedStyleAttributeValue:Invalid;". This variable is used as the output sentinel for unsupported or invalid-signature types.

*   If any error values are passed directly as arguments to SanitizeStyleAttributeValues, they must all be collected and returned as a single joined error (via errors.Join) before any CSS string processing occurs. Functions that return errors must also propagate those errors.

*   string inputs must be sanitized against CSS injection: characters such as '<' must be escaped as CSS unicode escapes (e.g. \00003C), a trailing semicolon must be appended if missing, and empty strings must produce no output.

*   templ.SafeCSS inputs must be HTML-escaped (e.g. '<' becomes '&lt;') but NOT CSS-sanitized, a trailing semicolon must be appended if missing, and empty values must produce no output.

*   map[string]string inputs must have keys sorted alphabetically in the output; both keys and values must be CSS-sanitized. Invalid or empty property names must appear as 'zTemplUnsafeCSSPropertyName' and invalid property values must appear as 'zTemplUnsafeCSSPropertyValue' in the output.

*   map[string]templ.SafeCSSProperty inputs must have keys sorted alphabetically; keys must be CSS-sanitized (invalid keys become 'zTemplUnsafeCSSPropertyName'); values must be HTML-escaped but not CSS-sanitized.

*   KeyValue[string, string] inputs must have both the key and the value CSS-sanitized. Empty or invalid property names must produce 'zTemplUnsafeCSSPropertyName' and invalid values must produce 'zTemplUnsafeCSSPropertyValue'.

*   KeyValue[string, bool] inputs with a false bool value must be entirely elided from the output. When the bool is true, the key string must be CSS-sanitized as a regular CSS string value.

*   KeyValue[templ.SafeCSS, bool] inputs with a false bool value must be entirely elided from the output. When the bool is true, the key must be HTML-escaped but not CSS-sanitized.

*   Zero-argument functions returning a single value must be handled by recursively processing the return value as a style attribute value. Zero-argument functions returning (value, error) must return the error if it is non-nil, otherwise the first return value is processed.

*   Functions with invalid signatures (more than two return values, or a second return value that is not the error type) must produce TemplUnsupportedStyleAttributeValue appended to the output string, NOT an error return.

*   Slice inputs (including typed slices and mixed []any slices) must be processed by applying style sanitization to each element in order. Nested slices must be supported.

*   Nil values in the input and nil or empty input (nil slice or zero-length variadic) must produce an empty string with no error.

*   Any input value of an unrecognised, unsupported type (e.g. an integer) must produce TemplUnsupportedStyleAttributeValue in the output string, not an error.

*   The parser must be updated so that using a Go expression in the style attribute (e.g. style={ value }) is no longer a parse/validation error. The element validator must not reject expression-typed style attributes.

*   The generator test package at generator/test-style-attribute must contain: a template_templ.go file exporting Button[T any](style T, text string) templ.Component which calls SanitizeStyleAttributeValues(style) to populate the style attribute; and an expected.html file containing exactly two lines: '<button style="background-color:blue;color:red;">Click me</button>' and '<button style="background-color: red;">Click me</button>'.


*   Interface details: Type: Function
Name: SanitizeStyleAttributeValues
Location: runtime/styleattribute.go
Signature: SanitizeStyleAttributeValues(values ...any) (string, error)
Description: Converts variadic style attribute values of supported types to a sanitized CSS string for use in HTML style attributes. Used by generated template code when rendering style expressions.

Type: Variable
Name: TemplUnsupportedStyleAttributeValue
Location: runtime/styleattribute.go
Signature: var TemplUnsupportedStyleAttributeValue = "zTemplUnsupportedStyleAttributeValue:Invalid;"
Description: The exact string appended to the output when an unsupported type or invalidly-signed function is passed as a style attribute value. Must have this exact name and value because the runtime test references it directly.

Type: Function
Name: Button
Location: generator/test-style-attribute/template_templ.go
Signature: Button[T any](style T, text string) templ.Component
Description: Generated templ component used by the generator render test. Renders two buttons: the first passes the style parameter to SanitizeStyleAttributeValues and writes the result into the style attribute; the second calls getFunctionResult() (a local function returning ("background-color: red", nil)) and passes it to SanitizeStyleAttributeValues. Package name is teststyleattribute.

Type: File
Name: expected.html
Location: generator/test-style-attribute/expected.html
Description: Expected HTML output file embedded by the generator render test via go:embed. Must contain exactly two lines:
  Line 1: <button style="background-color:blue;color:red;">Click me</button>
  Line 2: <button style="background-color: red;">Click me</button>

Note: The parser validation in parser/v2/types.go that currently rejects expression-typed attributes named "style" must be removed. Without this change, templates using style={ expression } will fail to parse.

Note: The output placeholder string for invalid CSS property names is "zTemplUnsafeCSSPropertyName" and for invalid property values is "zTemplUnsafeCSSPropertyValue". These exact strings appear in test expected-output assertions and must be produced by the SanitizeStyleAttributeValues implementation.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.