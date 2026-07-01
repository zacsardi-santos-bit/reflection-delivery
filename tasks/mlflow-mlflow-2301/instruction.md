Implement automatic logging for Keras model training in MLflow to capture early stopping callback information. Ensure that relevant parameters and metrics are logged when early stopping is used, and handle cases where early stopping does not occur or other callbacks are used.

*   Log early stopping parameters when an early stopping callback is used:
    *   Include 'patience' and 'monitor' as run parameters.
    *   Exclude 'verbose' and 'mode' from logging.
*   Log metrics when early stopping triggers and best weights are restored:
    *   Include 'stopped_epoch' and 'restored_epoch' as run metrics.
    *   Calculate 'restored_epoch' as stopped_epoch minus max(1, patience).
    *   Append an extra metric entry to the metric history for the restored epoch's performance.
    *   Ensure total metric history entries equal the number of training epochs plus one.
*   Handle TensorFlow 1.x environments:
    *   Use 'epoch_loss' as the metric key for the extra restored-epoch entry.
*   Log metrics when early stopping triggers without restoring best weights:
    *   Include only 'stopped_epoch' as a metric.
    *   Ensure metric history has exactly as many entries as the number of training epochs.
*   Log parameters and metrics when early stopping is configured but not triggered:
    *   Include 'patience' and 'monitor' as run parameters.
    *   Log 'stopped_epoch' as a metric with value 0.
    *   Exclude 'restored_epoch' from logging.
    *   Ensure metric history matches the number of training epochs.
*   Exclude early stopping logs when no early stopping callback is used:
    *   Do not log 'patience', 'monitor', 'verbose', 'mode', 'stopped_epoch', or 'restored_epoch'.
    *   Ensure metric history matches the number of training epochs.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.