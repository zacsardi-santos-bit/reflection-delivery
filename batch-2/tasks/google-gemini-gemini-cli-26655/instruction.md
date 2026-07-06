Upgrade the context memory system to use structured, machine-readable memory snapshots instead of free-form text summaries. Implement a memory structure that can be incrementally updated, with separate lists for active tasks, discovered facts, user constraints, and a rolling summary of recent events.

*   Implement the `formatNodesForLlm` function in `packages/core/src/context/utils/formatNodesForLlm.ts`:
    *   Accept an array of `ConcreteNode` objects and return a string transcript formatted as '[Turn N] [ROLE] [TYPE]: content\n'.
    *   Use node's text for text payloads, 'CALL: name({args})' for function calls, and '[SEMANTIC_WRAPPER (toolName)]: {json_response}' for function responses.
    *   Accept an `options` object with `maxToolResponseChars` (default 2000). Truncate tool responses exceeding this limit using 'prefix... [TRUNCATED N chars] ...suffix'.
    *   Default to 'SYSTEM' for undefined node roles.

*   Define and export the `SnapshotState` interface in `packages/core/src/context/utils/snapshotGenerator.ts`:
    *   Include fields: `active_tasks`, `discovered_facts`, `constraints_and_preferences`, and `recent_arc`.

*   Implement the `SnapshotGenerator` class in `packages/core/src/context/utils/snapshotGenerator.ts`:
    *   Constructor: `constructor(env: ContextEnvironment)`.
    *   Method: `synthesizeSnapshot(nodes: readonly ConcreteNode[], previousStateJson?: string, options?: { maxSummaryTurns?: number; maxStateTokens?: number }): Promise<string>`.
    *   Initialize an empty `SnapshotState` if no `previousStateJson` is provided.
    *   Call `env.llmClient.generateJson` with 'CURRENT MASTER STATE' in the prompt when `previousStateJson` is present.
    *   Merge the LLM's returned patch into the existing state, appending new data and processing deletions.
    *   Cap `recent_arc` at `maxSummaryTurns` entries (default 5).
    *   Return the previous state unmodified if `generateJson` throws an error or returns malformed data.
    *   Enforce a token budget (`maxStateTokens`, default 4000) by dropping the oldest items in priority order.

*   Implement and export the `findLatestSnapshotBaseline` function in `packages/core/src/context/utils/snapshotGenerator.ts`:
    *   Signature: `findLatestSnapshotBaseline(targets: readonly ConcreteNode[]): BaselineSnapshotInfo | undefined`.
    *   Scan targets in reverse to find the most recent SNAPSHOT node with a non-empty payload.text.

*   Ensure `StateSnapshotAsyncProcessor` and `StateSnapshotProcessor` use `findLatestSnapshotBaseline` to find a baseline snapshot and include its text in the `generateJson` prompt when necessary.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.