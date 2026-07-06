I'm working on improving the eval API in our server.

*   EvalSchemas.Table.Response must be a Zod-compatible schema exported from src/types/api/eval.ts that validates eval table response envelopes. It must accept objects with: table (object with head as a non-null object and body as an array), totalCount (number), filteredCount (number), filteredMetrics (nullable array of unknown items), config (record/object), author (nullable string), version (number), id (string), and stats (any/unknown). Extra fields must be passed through without being stripped.

*   EvalSchemas.Table.Response must perform shallow validation of the table.body field — it must only verify that body is an array without accessing individual array elements. This means iterating or indexing into body items during validation is not permitted.

*   EvalSchemas.Table.Response must reject (return failure or throw) when version is provided as a string instead of a number, and must reject when body is not an array.

*   EvalSchemas.SubmitRating.Response must be a Zod-compatible schema exported from src/types/api/eval.ts that validates objects of shape { message: string }. Parsing the input { message: 'ok' } must return { message: 'ok' }.

*   The GET /api/eval/:id/table endpoint must validate that the format query parameter, if provided, is one of the accepted export format values (csv or json). Any other string value must cause the route to return HTTP 400 with a response body containing an error field whose value includes the text 'format'.

*   The GET /api/eval/:id/table endpoint must validate that the limit and offset query parameters, if provided, are integers. Non-integer numeric strings (such as floating-point values) must cause the route to return HTTP 400 with a response body containing an error field whose value includes 'limit' or 'offset' respectively.

*   All three invalid query parameter validations (format, limit, offset) for GET /api/eval/:id/table must occur before any database lookup is attempted — the eval record must not be fetched when input validation fails.


*   Interface details: Type: Object
Name: EvalSchemas
Location: src/types/api/eval.ts
Description: Namespace object that groups all Eval API Zod schemas. Must contain a `Table` sub-object with a `Response` property, and a `SubmitRating` sub-object with a `Response` property.

Type: Schema (Zod)
Name: EvalSchemas.Table.Response
Location: src/types/api/eval.ts
Description: Zod schema that validates eval table API response envelopes. Must accept objects containing the following fields: `table` (an object with a `head` field that is a non-null object, and a `body` field that is an array), `totalCount` (number), `filteredCount` (number), `filteredMetrics` (nullable array of unknown items), `config` (a record/object), `author` (nullable string), `version` (number — not string), `id` (string), and `stats` (any/unknown). The schema must be shallow with respect to the `body` array — it must confirm `body` is an array without accessing individual array elements. Parsing must pass through extra fields without stripping them. Must reject inputs where `version` is a string or where `body` is not an array.

Type: Schema (Zod)
Name: EvalSchemas.SubmitRating.Response
Location: src/types/api/eval.ts
Description: Zod schema that validates submit-rating response envelopes. Must accept and return objects of shape `{ message: string }`. Parsing `{ message: 'ok' }` must return `{ message: 'ok' }`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.