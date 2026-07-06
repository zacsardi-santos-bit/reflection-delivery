## Description

The transformers library offers a variety of fixed-schedule learning rate schedulers, but lacks a built-in option that adaptively adjusts the learning rate in **both directions** based on observed training metrics. The only metric-aware scheduler currently available can only reduce the learning rate when training plateaus — it cannot capitalize on periods of consistent improvement by raising the learning rate.

We need a new adaptive scheduler that monitors a training metric and:
- **Increases** the learning rate when the metric consistently improves over a configurable number of consecutive evaluations.
- **Decreases** the learning rate when the metric stops improving (plateaus) for a configurable number of consecutive evaluations.

Additional required behaviors:
- The learning rate must stay within configurable lower and upper bounds.
- After each adjustment, a configurable cooldown (for decreases) or warmup (for increases) period should suppress further changes.
- Optional smoothing of the metric values via a rolling window before making decisions.
- An auto-reset mechanism that restores the initial learning rate after it has been stuck at the minimum for a configurable number of steps.
- Both "minimize metric" and "maximize metric" orientations.
- Full serialization support (save and restore state) with backward compatibility when loading partial state dicts from older checkpoints.
- Integration with the standard training configuration so it can be selected by name, and integration with the trainer so it is stepped on evaluation metrics rather than on every training step (matching how the existing plateau-reduction scheduler is handled).

## Why This Matters

Many training runs benefit from being able to both back off when stuck and accelerate when making progress. A scheduler that can do both reduces manual tuning and can lead to faster, more stable convergence.
