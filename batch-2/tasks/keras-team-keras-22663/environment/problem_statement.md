## Description

When compiling a Keras model that has multiple outputs with named outputs declared, the metric computation system does not correctly handle the case where the model's predictions are provided as an ordered list while the training labels are provided as a named dictionary (or vice versa). The system fails to use the declared output names to bridge these two different container structures, resulting in metrics being silently applied to the wrong outputs.

A related issue occurs with deeply nested model outputs: when output names are declared for such models, the metric result keys are not prefixed with those output names. This makes it impossible to identify which output a given metric measurement belongs to.

## Expected Behavior

- When predictions are a flat list and labels are a named dictionary (or vice versa), the declared output names should be used to correctly match each prediction to its corresponding label.
- When a metrics dictionary is provided with keys matching the declared output names (even if in a different order), the metrics should be evaluated and reported in the output names ordering — not alphabetically.
- For deeply nested model outputs, if output names are declared, each metric result key should be prefixed with its corresponding output name.
- For deeply nested model outputs without declared output names, metric result keys should remain unprefixed (current behavior).
- Outputs with different shapes (different number of features) should be handled correctly without requiring uniform shapes across all outputs.

## Why This Matters

Models with mixed-structure outputs and labels are common in real-world training setups. Without this fix, developers cannot reliably use named metrics with multi-output models when the label and prediction containers differ, leading to subtle bugs where the wrong metric is applied to the wrong output.
