Implement a kernel-based dimensionality reduction outlier detection algorithm in the pyod library. Ensure it follows the standard interface of existing detectors, supports configurable components, and optional sampling. Validate input parameters to prevent invalid configurations.

*   Implement the `KPCA` class in `pyod/models/kpca.py`.
    *   Inherit from `pyod.models.base.BaseDetector`.
    *   Ensure compatibility with scikit-learn's `clone()` function.
    *   Constructor parameters:
        *   `contamination`: float, default=0.1
        *   `n_components`: int or None, default=None
        *   `n_selected_components`: int or None, default=None
        *   `sampling`: bool, default=False
        *   `subset_size`: float in (0, 1] or int in (0, n_samples], default=20
        *   `random_state`: int, RandomState, or None, default=None
        *   Additional kernel-related parameters inherited from `KernelPCA`.

*   Implement the following methods:
    *   `fit(X, y=None) -> self`
        *   Fit the model on training data `X`.
        *   Set `decision_scores_`, `labels_`, and `threshold_` attributes.
        *   Validate parameters:
            *   Raise `ValueError` if `sampling=True` and `subset_size` is a float not in (0.0, 1.0] or an int not in (0, n_samples].
            *   Raise `ValueError` if `n_components < 1`.
            *   Raise `ValueError` if `n_selected_components` is specified and not in [1, `n_components`].
    *   `decision_function(X) -> np.ndarray`
        *   Return anomaly scores for test data `X`.
        *   Ensure ROC AUC >= 0.8 on standard benchmark data.

*   Ensure the following attributes are set after `fit()`:
    *   `decision_scores_`: np.ndarray of shape (n_train,)
    *   `labels_`: np.ndarray
    *   `threshold_`: float

*   Utilize methods from `BaseDetector`:
    *   `predict(X, return_confidence=False) -> np.ndarray or (np.ndarray, np.ndarray)`
    *   `predict_proba(X, method='gaussian', return_confidence=False) -> np.ndarray or (np.ndarray, np.ndarray)`
    *   `fit_predict(X) -> np.ndarray`
    *   `fit_predict_score(X, y, scoring='roc_auc_score') -> float`
    *   `_predict_rank(X, normalized=False) -> np.ndarray`

*   Validate `subset_size` and component parameters:
    *   `subset_size` must be strictly greater than 0 and at most 1.0 if float, or greater than 0 and at most `n_samples` if int.
    *   Raise `ValueError` for invalid `subset_size` values like 1.5, 0, `n_samples`+100, or -1.
    *   Raise `ValueError` if `n_components` is negative.
    *   Ensure `n_selected_components` is within valid range relative to `n_components`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.