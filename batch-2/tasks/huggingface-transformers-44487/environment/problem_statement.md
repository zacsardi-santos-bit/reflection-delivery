## Description

When training models on Kubeflow infrastructure, there is currently no built-in way for the trainer to report progress back to the Kubeflow platform. The platform has no visibility into how far along training is, what metrics are being produced, or how much time remains — users must manually instrument this or go without any progress tracking from the platform's perspective.

## Expected Behavior

- A new training callback should be added that can push training progress updates to a Kubeflow server during training. It should report progress as a percentage, include an estimated time remaining, and forward training metrics (such as loss and learning rate).
- The callback should only report progress on the main process in distributed training setups.
- Progress should be reported at training start (0%), updated throughout training steps, and finalized at 100% when training ends.
- Progress percentage during intermediate steps should be capped — it should not reach 100% until training explicitly completes.
- Only numeric metric values should be forwarded to the server; non-numeric log entries should be silently ignored.
- Status updates should be rate-limited to avoid overwhelming the server with requests — individual updates may be skipped if they happen too frequently, but certain updates (like start and end) should always be sent regardless.
- The callback should gracefully skip the network call and report failure when the server URL is not configured in the environment.
- Authentication tokens should be read from a configured file path and cached to avoid redundant reads.
- When a Kubeflow server URL is detected in the environment, the training configuration should automatically enable this integration without requiring users to explicitly configure it. This auto-detection should work even if the user specified no integrations, and it should add at most one entry even if called multiple times.

## Why This Matters

Users running training jobs on Kubeflow infrastructure currently get no feedback in the Kubeflow UI about training progress. This makes it difficult to monitor long-running jobs, estimate completion time, or detect stalled training. Automatically enabling this integration when running inside a Kubeflow environment removes the need for any manual configuration.
