## Description

MLflow's model evaluation feature does not support evaluating models served as deployment endpoints. There is no utility to detect whether a given model URI refers to a deployment endpoint (as opposed to a model registry URI or other URI types), and when users attempt to evaluate against such endpoints, the system does not validate input data properly.

## Expected Behavior

- There should be a utility that can detect whether a model URI refers to a deployment endpoint, returning a positive result for such URIs and a negative result for all other URI types or when no model is provided.
- When evaluating against a deployment endpoint, the system should validate the shape and type of input data and raise a clear, descriptive error when:
  - The input data does not have exactly one input column (e.g., too many columns or no input columns at all)
  - The input column contains values that are not plain text strings or dictionary-like objects

## Why This Matters

Without proper endpoint detection and input validation, users who try to evaluate models served at deployment endpoints receive confusing or unhelpful errors. Adding this utility and validation allows the system to give clear, actionable error messages when input data is malformed, and enables the broader evaluation pipeline to handle deployment endpoints correctly.
