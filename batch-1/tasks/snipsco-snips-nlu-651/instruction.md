Implement a `fitted_required` decorator to ensure consistent "not fitted" error handling across processing units in the Snips NLU library. Update the CRFSlotFiller to handle intents without slots by skipping unnecessary CRF training and returning appropriate results.

Requirements:

*   Implement the `fitted_required` decorator in `snips_nlu/utils.py`:
    *   Signature: `fitted_required(func)`
    *   Ensure it checks if `self.fitted` is `True` before executing the wrapped method.
    *   Raise `NotTrained` exception with the message "{unit_name} must be fitted" if `self.fitted` is `False`.

*   Update error handling for processing units:
    *   Raise `NotTrained` exception when `CRFSlotFiller.get_slots()`, `get_sequence_probability()`, or `log_weights()` is called before fitting.
    *   Raise `NotTrained` exception when `LogRegIntentClassifier.get_intent()` is called before fitting.
    *   Raise `NotTrained` exception when `DeterministicIntentParser.parse()` is called before fitting.
    *   Raise `NotTrained` exception when `ProbabilisticIntentParser.parse()` is called before fitting.
    *   Raise `NotTrained` exception when `SnipsNLUEngine.parse()` is called before fitting.

*   Update `CRFSlotFiller` in `snips_nlu/slot_filler/crf_slot_filler.py`:
    *   Ensure `crf_model` is `None` when fitted with a dataset that has no slots.
    *   Ensure `get_slots()` returns an empty list without calling `compute_features()` for datasets with no slots.
    *   Ensure `get_sequence_probability()` returns `1.0` if all labels are 'O' and `0.0` if any label is not 'O'.
    *   Ensure `fitted` property returns `True` if `slot_name_mapping` is not `None`, even if `crf_model` is `None`.
    *   Ensure the slot filler is serializable and deserializable when fitted with a dataset that has no slots.
    *   Ensure the slot filler returns an empty slots list after deserialization.

*   Ensure the `fitted` property returns `False` for all processing units (CRFSlotFiller, LogRegIntentClassifier, DeterministicIntentParser, ProbabilisticIntentParser, SnipsNLUEngine) before fitting.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.