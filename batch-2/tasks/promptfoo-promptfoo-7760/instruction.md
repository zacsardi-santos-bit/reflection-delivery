I'm working on fixing how author attribution is handled during evaluations in a few different scenarios.

*   The getAuthor function must accept an optional override parameter (a string or null). When the user is NOT logged into cloud, the resolution priority is: the override parameter first, then the stored user email, then the PROMPTFOO_AUTHOR environment variable, and finally null.

*   When the user IS logged into cloud (a cloud API key is present in the global config), getAuthor must prioritize the stored cloud account email over the override parameter, then fall back to the override, then the PROMPTFOO_AUTHOR environment variable, then null.

*   Eval.create must accept an optional third opts parameter with an author field typed as string or null. When opts.author is explicitly provided (including an explicit null value), Eval.create must use it directly and must NOT call getAuthor.

*   When Eval.create is called without an opts parameter (or without opts.author set), it must call getAuthor() with no arguments to determine the author and persist that value.

*   The evaluate function must pass testSuite.author as the override argument to getAuthor, and pass the resolved result to Eval.create via the opts.author field. The author field must NOT be included in the config object (the first argument) passed to Eval.create.

*   The eval command (doEval) must call getAuthor and set the resolved author on the eval record even when the --no-write flag is active.

*   The createShareableUrl function must skip email lookup entirely when the eval record's author field is already set to a non-null value.

*   The createShareableUrl function must still look up and backfill the author from the stored user email (and save the record) when the eval's author is null and the process is running in an interactive TTY.

*   The import command must preserve the author from the imported eval's metadata by passing it explicitly to Eval.create, bypassing getAuthor resolution. This must hold even when the local user is authenticated to the cloud as a different identity.


*   Interface details: Type: Function
Name: getAuthor
Location: src/globalConfig/accounts.ts
Signature: getAuthor(override?: string | null): string | null
Description: Returns the resolved author string for an evaluation. Accepts an optional override hint. When NOT logged into cloud: override → stored user email (account.email in global config) → PROMPTFOO_AUTHOR env var → null. When logged into cloud (cloud.apiKey present in global config): stored cloud account email → override → PROMPTFOO_AUTHOR env var → null.

Type: Class
Name: Eval
Location: src/models/eval.ts
Description: Represents a stored evaluation record. The static create method is updated to accept a new optional third parameter.
Signature: static create(config: Partial<UnifiedConfig>, renderedPrompts: Prompt[], opts?: { author?: string | null }): Promise<Eval>
Description: Creates and persists an Eval. When opts.author is explicitly provided (including null), that value is used directly and getAuthor is NOT called. When opts is omitted entirely, getAuthor() is called with no arguments to determine the author.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.