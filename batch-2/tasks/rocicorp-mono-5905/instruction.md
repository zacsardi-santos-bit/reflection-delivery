I'm working on the server-side request handling layer and need to refactor the mutation and query processing functions to use an object-based calling convention instead of positional parameters.

*   handleMutateRequest must accept a single options object with named fields: dbProvider, handler, query, body, and userID (plus optional logLevel). Alternatively, it may accept a Request object instead of query+body.

*   When handleMutateRequest is called in object form with userID set to null, the response object must include a userID field with the value null.

*   When handleMutateRequest is called in object form with a string userID, the response must echo that same string in the userID field.

*   When handleMutateRequest is called in object form with userID set to undefined, it must normalize it to null and include userID: null in the response.

*   When handleMutateRequest is called using the legacy positional signature (db, handler, queryOrRequest, body) without providing a userID, the response must NOT include a userID property.

*   handleQueryRequest must accept a single options object with named fields: handler, schema, body, and userID (plus optional logLevel and query). Alternatively, it may accept a Request object instead of body.

*   When handleQueryRequest is called in object form with userID set to null, the response must include a userID field with the value null.

*   When handleQueryRequest is called in object form with a string userID, the response must echo that same string in the userID field.

*   When handleQueryRequest is called in object form with userID set to undefined, it must normalize it to null and include userID: null in the response.

*   When handleQueryRequest is called using the legacy positional signature (handler, schema, bodyOrRequest) without providing a userID, the response must NOT include a userID property.

*   Parse error responses from handleQueryRequest must include a message containing the string 'Failed to parse query request' (not 'Failed to parse getQueries request').


*   Interface details: Type: Function
Name: handleMutateRequest
Location: packages/zero-server/src/process-mutations.ts
Signature (new object form): handleMutateRequest(options: {dbProvider: Database, handler: MutateRequestHandler, query?: QueryParams, body?: ReadonlyJSONValue, request?: Request, userID: string | null | undefined, logLevel?: LogLevel}) -> Promise<MutateResponse>
Signature (legacy positional form with body): handleMutateRequest(db: Database, handler: MutateRequestHandler, query: QueryParams, body: ReadonlyJSONValue, logLevel?: LogLevel) -> Promise<MutateResponse>
Signature (legacy positional form with Request): handleMutateRequest(db: Database, handler: MutateRequestHandler, request: Request, logLevel?: LogLevel) -> Promise<MutateResponse>
Description: Processes a mutation push request. The new object-form signature bundles all arguments (including a mandatory userID field) into a single options object. The legacy positional forms accept arguments in order and omit userID from the response. Both forms remain supported. The new object form always includes userID in the response (null if userID was null or undefined); the legacy form never includes it.

Type: Function
Name: handleQueryRequest
Location: packages/zero-server/src/queries/process-queries.ts
Signature (new object form with body): handleQueryRequest(options: {handler: QueryRequestHandler, schema: Schema, query?: object, body: ReadonlyJSONValue, userID: string | null | undefined, logLevel?: LogLevel}) -> Promise<QueryResponse>
Signature (new object form with Request): handleQueryRequest(options: {handler: QueryRequestHandler, schema: Schema, request: Request, userID: string | null | undefined, logLevel?: LogLevel}) -> Promise<QueryResponse>
Signature (legacy positional form with body): handleQueryRequest(handler: QueryRequestHandler, schema: Schema, body: ReadonlyJSONValue, logLevel?: LogLevel) -> Promise<QueryResponse>
Signature (legacy positional form with Request): handleQueryRequest(handler: QueryRequestHandler, schema: Schema, request: Request, logLevel?: LogLevel) -> Promise<QueryResponse>
Description: Processes a query transform request. The new object-form signature bundles all arguments (including a mandatory userID field) into a single options object. The legacy positional forms accept arguments in order and omit userID from the response. The handler callback (QueryRequestHandler) receives (name: string, args: ReadonlyJSONValue | undefined) and returns a query object directly. Parse errors include a message containing 'Failed to parse query request'. The new object form always includes userID in the response (null if userID was null or undefined); the legacy form never includes it.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.