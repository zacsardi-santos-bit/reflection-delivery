# Stream Error Leaves Session in Unrecoverable State

## Description

When the model API returns an error during a streaming conversation turn, the agent signals the failure to the caller but does not properly clean up the current task or release the session. As a result, the conversation becomes stuck: any subsequent user input is ignored or queued indefinitely, making it impossible to continue the conversation after an error.

## Expected Behavior

- When a streaming/API error occurs during a turn, the system should emit an error notification **and** a task completion signal, even though the turn failed.
- After the failed turn is concluded, the session should be fully released so the user can immediately send another message.
- A follow-up message sent after the error should be accepted, processed normally, and produce its own task completion signal.

## Current Behavior

After an API/stream error, the system emits an error event but never emits a task completion event. The running task is not cleared, so the session remains locked. Subsequent user inputs cannot be processed.

## Why This Matters

Users encountering transient API errors should be able to retry or continue their conversation without having to restart the entire session. Right now, a single stream error renders the session permanently unusable.
