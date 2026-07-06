## Description

The Snips NLU library has two related issues:

1. **Inconsistent "not fitted" error handling**: Different processing units (SnipsNLUEngine, CRFSlotFiller, LogRegIntentClassifier, DeterministicIntentParser, ProbabilisticIntentParser) each implement their own "not fitted" checks manually within their methods. This leads to code duplication and inconsistent error messages.

2. **CRFSlotFiller fails when trained on intents without slots**: When training a CRFSlotFiller on an intent that has no slots (utterances with only plain text), the slot filler still attempts to train a CRF model, which is unnecessary and may cause issues during serialization/deserialization.

## Expected Behavior

- All processing units should consistently raise a `NotTrained` exception when their primary methods (parse(), get_slots(), get_intent()) are called before the unit has been fitted
- The `fitted` property should correctly return `False` for all unfitted processing units
- When CRFSlotFiller is fitted with a dataset containing an intent without slots:
  - The CRF model should not be trained (crf_model should be None)
  - `get_slots()` should return an empty list without attempting CRF inference
  - `get_sequence_probability()` should return 1.0 for all "O" (Outside) labels and 0.0 for any other labels
  - The slot filler should be serializable and deserializable
  - `log_weights()` should return an appropriate message indicating no weights to display

## Current Behavior

- Each processing unit has duplicated inline checks for the fitted state
- CRFSlotFiller.fitted property only returns True when crf_model is not None AND crf_model.tagger_ is not None, which breaks for intents without slots
- No special handling for datasets/intents that have no slots, leading to unnecessary CRF training attempts
