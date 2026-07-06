I'm working on the Backstage search backend plugin and need to add a new action to the backend actions registry that lets agents query the search engine.

*   The createQueryAction function must accept an object with engine, actionsRegistry, searchIndexService, and logger properties, and register a search query action with the actionsRegistry without throwing, even when searchIndexService.getDocumentTypes() returns an empty object.

*   The registered action must accept input with a required term (string) field and optional types (string array), filters (nested object), pageLimit (number), and pageCursor (string) fields, and forward all of these fields as-is to the engine's query method.

*   The registered action's output must include a results array, an optional nextPageCursor string, an optional totalItems number (mapped from numberOfResults in the engine response), and a hasMoreResults boolean.

*   hasMoreResults must be true when the engine response contains a nextPageCursor, and false when nextPageCursor is absent.

*   The authorization field must be stripped from every result document before it is included in the output results array.

*   Results whose document location uses a protocol other than http: or https: (such as javascript: or data:) must be excluded from the output results. For each excluded result, logger.info must be called with a message that contains the unsafe URL (e.g. a string containing 'javascript:' or 'data:').


*   Interface details: Type: Function
Name: createQueryAction
Location: plugins/search-backend/src/actions/createQueryAction.ts
Signature: createQueryAction({ engine, actionsRegistry, searchIndexService, logger }: { engine: SearchEngine; actionsRegistry: ActionsRegistryService; searchIndexService: SearchIndexService; logger: LoggerService; }): void
Description: Registers a search query action with the provided actions registry. The action accepts { term: string, types?: string[], filters?: JsonObject, pageLimit?: number, pageCursor?: string } as input and returns { results: Array<{ type: string, document: { title: string, text: string, location: string, [key: string]: any } }>, nextPageCursor?: string, totalItems?: number, hasMoreResults: boolean } as output. Authorization fields are stripped from documents, results with unsafe location protocols are filtered out (logging each exclusion via logger.info with a message containing the protocol), and all input fields are forwarded to engine.query.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.