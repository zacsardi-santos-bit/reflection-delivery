I'm working on a semantic segmentation model and I need the sparse categorical crossentropy metric to support ignoring a specific class label.

*   The SparseCategoricalCrossentropy metric class must accept an ignore_class parameter (integer or None, default None) in its constructor, in addition to the existing from_logits, axis, name, and dtype parameters.

*   When ignore_class is set to an integer value, samples whose true label equals that integer must be excluded from the metric computation entirely — they should not contribute to the numerator or denominator of the mean.

*   When ignore_class is set and sample_weight is also provided, the weighted mean must be computed correctly over only the non-ignored samples.

*   When ignore_class is set alongside from_logits=True, the metric must correctly compute the loss from raw logit inputs while still excluding the specified class.


*   Interface details: Type: Class
Name: SparseCategoricalCrossentropy
Location: keras/src/metrics/probabilistic_metrics.py
Description: Sparse categorical crossentropy metric that computes the mean cross-entropy loss between true integer labels and predicted probability distributions or logits. Must be updated to accept an ignore_class parameter.
Signature: __init__(name="sparse_categorical_crossentropy", dtype=None, from_logits=False, ignore_class=None, axis=-1)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.