I'm working on the Konnect integration in Insomnia.

*   The sanitizeRoute function must be a named export from packages/insomnia/src/konnect/transform.ts.

*   When the route contains no template syntax, sanitizeRoute must return the route object unchanged (equal by value).

*   sanitizeRoute must strip {{ ... }} expressions (including their delimiters) from the name field, leaving surrounding text intact. For example, 'Route {{ env.SECRET }}' becomes 'Route '.

*   sanitizeRoute must strip {{ ... }} expressions from each entry in the paths array, keeping any surrounding text. For example, '/api/{{ env.SECRET }}/users' becomes '/api//users'.

*   sanitizeRoute must strip {{ ... }} expressions from each entry in the hosts array, keeping any surrounding text. For example, '{{ env.SECRET }}.test.com' becomes '.test.com'.

*   For the methods array: entries that are entirely a {{ ... }} expression (so the full string is stripped to empty) must be filtered out. If all entries are removed, the methods field must be set to null. If some entries remain non-empty after stripping, only those valid entries are kept.

*   For the headers object: any header entry whose value array becomes entirely empty after stripping must be dropped from the returned headers object. Any header entry whose key/name becomes entirely empty after stripping must also be dropped.

*   sanitizeRoute must strip {% ... %} block tag syntax from string fields, in addition to {{ ... }} variable syntax.

*   sanitizeRoute must handle delimiter interleaving: a {% %} tag nested inside {{ }} syntax must be stripped, and a {{ }} tag nested inside {% %} syntax must also be stripped, preventing template injection via interleaved delimiters.

*   Unpaired delimiters (an opening {{ or {% with no corresponding closing delimiter) must be left intact and not modified.

*   sanitizeRoute must strip {{ ... }} expressions from the expression field, keeping surrounding text intact.

*   sanitizeRoute must handle null values for name, methods, paths, hosts, headers, and expression fields without throwing an error, returning those fields as null in the result.


*   Interface details: Type: Function
Name: sanitizeRoute
Location: packages/insomnia/src/konnect/transform.ts
Signature: sanitizeRoute(route: KonnectRoute) -> KonnectRoute
Description: Accepts a route object and returns a new route object with all Nunjucks-style template expressions removed from its string fields. The input route has the shape: { id: string, protocols: string[] | null, snis: any, service: any, name: string | null, methods: string[] | null, paths: string[] | null, hosts: string[] | null, headers: Record<string, string[]> | null, expression: string | null }. The function must be exported as a named export from the transform module.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.