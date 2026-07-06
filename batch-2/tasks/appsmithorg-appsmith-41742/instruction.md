I'm running into a data corruption issue when users upload binary files like PDFs through our REST API integration.

*   The render method in MustacheHelper must NOT decode HTML entities (such as &lt;, &gt;, &amp;, &#xA;, &#xD;, &#9;) that appear in binding values. These entities must pass through to the rendered output exactly as provided.

*   The render method must convert only &quot; and &#34; entities in binding values to escaped double quotes (\" ) for JSON validity. No other HTML entities in binding values should be transformed.

*   Template literal text (non-binding portions of the template string) must never be modified. Any HTML entities in the template literal, including &quot;, must remain unchanged in the rendered output.

*   When a binding value contains &quot;, the rendered output must contain \" at that position. When the template literal contains &quot;, the rendered output must still contain &quot; at that position.

*   The render method must return binary data binding values unchanged. If the input value contains &#xA;, the output must contain &#xA; and must NOT contain a newline character (\n) at that position.

*   The parseMultipartFileData method in DataUtils must correctly process file properties whose values are JSON arrays of file metadata objects with name, type, and data fields. The resulting multipart request body must contain Content-Disposition headers with the field name and filename, and a Content-Type header with the MIME type.

*   When a file property value is a JSON array containing binary file data with HTML entity sequences (e.g., &#xA;), the parseMultipartFileData method must preserve those entity sequences in the multipart body without modification.

*   When a file property value consists of a JSON array preceded by leading whitespace or newline characters, the parseMultipartFileData method must still correctly identify and process it as a multipart JSON file array, producing the correct Content-Disposition and Content-Type headers.


*   Interface details: Type: Class
Name: MustacheHelper
Location: app/server/appsmith-interfaces/src/main/java/com/appsmith/external/helpers/MustacheHelper.java
Description: Utility class for rendering mustache-style templates. The render method substitutes binding values from a key-value map into template strings delimited by {{ and }}.
Signature: render(String template, Map<String, String> keyValueMap) -> String

Type: Class
Name: DataUtils
Location: app/server/appsmith-interfaces/src/main/java/com/appsmith/external/helpers/restApiUtils/helpers/DataUtils.java
Description: Utility class for building HTTP request bodies. The parseMultipartFileData method builds a multipart form body from a list of Property objects, where properties of type "file" contain JSON-encoded file metadata.
Signature: parseMultipartFileData(List<Property> properties) -> BodyInserter<?, ?>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.