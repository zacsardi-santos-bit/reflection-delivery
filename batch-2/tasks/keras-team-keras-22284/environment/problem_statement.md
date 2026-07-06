## Description

The sparse categorical crossentropy metric does not currently support ignoring a specific class during computation. In many real-world tasks — such as semantic segmentation — certain label values represent unlabeled, void, or background regions that should not contribute to the training metric. Right now, users have no built-in way to exclude these labels, and must implement custom workarounds.

## Expected Behavior

- The metric should accept an optional parameter that specifies a single class label to ignore during computation.
- When this parameter is provided, any samples whose ground-truth label matches the ignored class should be completely excluded from the metric — they should not affect the computed score in any way.
- This feature should work correctly when outputs are raw logits rather than probabilities.
- This feature should also work correctly when per-sample weights are provided alongside the predictions.

## Why This Matters

Without this capability, users working on tasks with partially-labeled data (e.g., semantic segmentation with void/background classes) must manually filter or mask their data before passing it to the metric. Adding native support for a class-ignore parameter makes the metric more practical for these common use cases and avoids error-prone preprocessing workarounds.
