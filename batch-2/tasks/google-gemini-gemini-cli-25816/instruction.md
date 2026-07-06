Implement the necessary updates to the session memory system to address issues with session file handling, sorting, and extraction. Ensure that the system can process both legacy and new line-delimited session formats, correctly sort sessions, and manage extraction states.

*   Update the `Config` class in `packages/core/src/config/config.ts`:
    *   Implement `isMemoryV2Enabled()` to return `true` by default unless `experimentalMemoryV2` is explicitly set to `false` in `ConfigParameters`.

*   Modify the `ExtractionRun` interface in `packages/core/src/services/memoryService.ts`:
    *   Add optional fields `candidateSessions` and `processedSessions` to track session extraction attempts and successes.

*   Enhance `startMemoryService` in `packages/core/src/services/memoryService.ts`:
    *   Record `candidateSessions` and `processedSessions` in the `ExtractionState`.
    *   Ensure `sessionIds` only includes successfully processed sessions.
    *   Exclude file reads outside the `chats` directory from being marked as processed.

*   Update `buildSessionIndex` in `packages/core/src/services/memoryService.ts`:
    *   Support JSONL session files with metadata in the first line.
    *   Sort sessions by `lastUpdated` from file content, not by filename.
    *   Mark sessions as `[NEW]` if `lastUpdated` is newer than the previous run's `runAt`.
    *   Rotate older unprocessed sessions into the current batch to prevent starvation.
    *   Include older unprocessed sessions in `newSessionIds` if recent files are already processed.

*   Modify `getPreviousSession` in `packages/core/src/services/sessionSummaryUtils.ts`:
    *   Support JSONL files and sort by `lastUpdated` from file content.
    *   Skip the active session identified by `config.getSessionId()`.
    *   Implement early termination based on filesystem `mtime`.
    *   Use `loadConversationRecord` with `{ metadataOnly: true }` for candidate files.

*   Update `generateSummary` in `packages/core/src/services/sessionSummaryUtils.ts`:
    *   For JSONL files, append a line with `{ "$set": { "summary": "<generated summary>" } }` without rewriting the file.
    *   For legacy JSON files, update the summary field and rewrite the file, preserving `lastUpdated`.
    *   Preserve newer `lastUpdated` when concurrently modified JSONL files are updated.
    *   Skip the active session when summarizing.

*   Ensure `loadConversationRecord` in `packages/core/src/services/chatRecordingService.ts`:
    *   Supports loading metadata only when `options.metadataOnly` is `true`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.