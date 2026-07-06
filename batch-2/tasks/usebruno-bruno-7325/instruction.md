I'm working on the Bruno-to-Postman collection exporter and I've found two bugs that produce incorrect output.

*   When converting a Bruno collection to Postman format, file-type fields in multipart form data must have their src property set to a plain string (the file path), not an array containing the file path.

*   When a file-type multipart form field has no file path (empty or missing value), the src property in the Postman output must be null, not an empty array.

*   When converting a Bruno request that uses the GET or HEAD HTTP method and also has a request body, the brunoToPostman function must add a protocolProfileBehavior object with disableBodyPruning set to true on the resulting Postman item.

*   When converting a Bruno request that uses POST or other non-GET/HEAD methods with a body, no protocolProfileBehavior property should be added to the resulting Postman item.

*   When converting a Bruno GET or HEAD request that has no body, no protocolProfileBehavior property should be added to the resulting Postman item.


*   Interface details: Type: Function
Name: brunoToPostman
Location: packages/bruno-converters/src/postman/bruno-to-postman.js
Signature: brunoToPostman(brunoCollection) -> PostmanCollection
Description: Converts a Bruno collection object to a Postman collection object. For multipart form file-type fields, the src property in the output must be a plain string (or null if no path is set), not an array. For request items using GET or HEAD methods with a body, the output item must include a protocolProfileBehavior property set to { disableBodyPruning: true }. For POST and other methods with a body, or GET/HEAD without a body, protocolProfileBehavior must not be set.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.