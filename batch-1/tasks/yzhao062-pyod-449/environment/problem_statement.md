## Description

The pyod library is missing an outlier detection algorithm based on kernel-based dimensionality reduction. This technique projects data into a lower-dimensional space using a kernel function and uses the reconstruction error as the anomaly score — points that are difficult to reconstruct are considered anomalies.

## Expected Behavior

- A new outlier detector using kernel-based dimensionality reduction should be available in the library, following the same interface as all other detectors.
- Users should be able to control how many principal components to compute and how many of those to actually use when scoring.
- The detector should support an optional sampling mode where only a random subset of training data is used to fit the underlying transformation. Both fractional (proportion of training data) and absolute (fixed number of samples) subset sizes should be accepted.
- Invalid configuration should be caught early: negative or out-of-range component counts should raise an informative error, and invalid subset sizes (out of bounds fractions, non-positive integers, or integers exceeding the dataset size) should also raise errors.
- The detector should support all standard pyod interfaces: probability estimation with multiple methods, confidence scores, rank-based prediction, and scikit-learn compatibility (cloning).

## Why This Matters

Having a kernel-based dimensionality reduction approach available in pyod expands the range of anomaly detection strategies available to users and complements existing linear approaches. Users working with non-linearly separable data can benefit from this detector's ability to capture more complex structure in the data.
