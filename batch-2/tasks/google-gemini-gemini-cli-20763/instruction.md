Implement a "two-strike" loop detection policy in the agent loop detection system to allow agents a chance to self-correct before terminating a session. Modify the loop detection service to provide a numeric count of loop detections and update the session handling logic accordingly.

*   Update `LoopDetectionService` in `packages/core/src/services/loopDetectionService.ts`:
    *   Change `addAndCheck(event: ServerGeminiStreamEvent)` to return an object with a numeric `count` field, optional `type`, and `detail` fields.
        *   Return `{ count: 0 }` when no loop is detected.
        *   Return `{ count: 1, detail?: string }` on the first loop detection.
        *   Return `{ count: 2, detail?: string }` after `clearDetection()` and a second detection.
    *   Change `turnStarted(signal: AbortSignal)` to return a Promise resolving to an object with a numeric `count` field.
        *   Return `{ count: 0 }` when no loop is detected.
        *   Return `{ count: 1 }` when a loop is detected.
    *   Implement `clearDetection()` to clear the active loop detection state while preserving the cumulative strike count.
    *   Implement `reset(promptId: string, text?: string)` to reset the detector for a new prompt.

*   Update `sendMessageStream` in `packages/core/src/core/client.ts`:
    *   On detecting a loop with `count === 1` (Strike 1):
        *   Call `loopDetector.clearDetection()`.
        *   Do not emit a `LoopDetected` event.
        *   Recursively call `sendMessageStream` with a recovery message starting with "System: Potential loop detected" and including the detection `detail`.
        *   If `boundedTurns` would reach 0, skip recovery and emit `MaxSessionTurns`.
    *   On detecting a loop with `count >= 2` (Strike 2):
        *   Emit a `LoopDetected` event and terminate the session.
    *   On processing a new `promptId`, call `loopDetector.reset(promptId, text)` where `text` is the first part of the message array.

*   Modify constants in `packages/core/src/services/loopDetectionService.ts`:
    *   Set `LLM_CHECK_AFTER_TURNS` to 30.
    *   Set the minimum interval for LLM loop checks to 5 turns, using the formula: `5 + (15 - 5) * (1 - confidence)`.

*   Ensure `LoopType` is exported from `packages/core/src/telemetry/types.ts`, including values `TOOL_CALL_LOOP` and `LLM_DETECTED_LOOP`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.