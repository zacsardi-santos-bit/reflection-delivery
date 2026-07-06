## Description

MLflow's automatic Keras training logging does not currently capture any information about early stopping callbacks. When a user trains a model with early stopping enabled, the MLflow run contains no record of the early stopping configuration or what epoch the model's weights were restored from. This makes it difficult to understand why training ended when it did and which checkpoint's performance is reflected in the logged metrics.

## Expected Behavior

- When an early stopping callback is present during training, the patience setting and the metric being monitored should be automatically logged as run parameters.
- When training actually stops early and best weights are restored, the epoch at which training stopped and the epoch whose weights were restored should be logged as run metrics.
- An extra metric entry should be appended to the metric history to record the performance at the restored (best) epoch, making it easy to see the final model's quality directly from the metric history.
- When training stops early but best weights are not restored, only the stopped epoch is recorded — no extra metric entry should be appended.
- When early stopping is configured but training runs to completion (the patience is never exhausted), the stopped epoch should be logged as zero to indicate that no stopping occurred, without logging a restored epoch.
- When a non-early-stopping callback is used, none of the early stopping fields should be logged.

## Why This Matters

Users who rely on early stopping to prevent overfitting need a complete picture of their run in MLflow: what triggered the early stop, what the best epoch was, and what performance the restored model actually achieved. Without this, comparing runs that used different patience settings or determining the true best model performance requires going back to training logs manually.
