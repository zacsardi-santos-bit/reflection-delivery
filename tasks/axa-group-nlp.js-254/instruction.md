Implement a new module to support an alternative entity recognition backend in the NLP library by integrating with an external HTTP service. Ensure that the module handles various entity types and gracefully manages service failures. Update the NLP manager to route entity recognition through this new module when configured accordingly.

*   Implement the `NerDuckling` class in `lib/ner/ner-duckling.js`:
    *   Export `NerDuckling` as the default module export.
    *   Constructor:
        *   Accept an optional `settings` object with a `ducklingUrl` string property.
        *   Default URL to `http://localhost:8000/parse` if no settings are provided.
        *   Set `this.url` to a parsed URL object with `.href` reflecting the full URL.
        *   Set `this.port` to:
            *   443 for HTTPS URLs without an explicit port.
            *   80 for HTTP URLs without an explicit port.
            *   The explicit port string if present in the URL.
    *   Implement `getLocale(language: string) -> string`:
        *   Convert a 2-character language code to a culture locale string in 'xx-XX' format.
    *   Implement `request(utterance: string, language: string) -> Promise`:
        *   Return a Promise resolving to the raw parsed response from the Duckling service.
    *   Implement `findBuiltinEntities(utterance: string, language: string) -> Promise<{ edges: object[], source: object[] }>`:
        *   Call `this.request()`, transform each entity, and return `{ edges, source }`.
        *   On exceptions, return `{ edges: [] }` without throwing errors.

*   Update `NlpManager`:
    *   When configured with `useDuckling: true`, ensure `manager.process()` resolves with an empty entities array if the external service call raises an exception.
    *   Ensure `nerManager.nerRecognizer` is a `NerDuckling` instance when `ner: { useDuckling: true }`.

*   Ensure entity extraction and transformation:
    *   For each entity type (email, phone-number, url, number, distance, quantity, temperature, volume, amount-of-money, duration, time), map to the correct entity type name and structure the resolution object appropriately.
    *   Include start, end, len, accuracy, sourceText, and utteranceText for all entities.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.