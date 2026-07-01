I'm working on the Snips NLU library and I've noticed that the "not fitted" error handling is inconsistent across different components. Each processing unit like SnipsNLUEngine, CRFSlotFiller, and the intent classifiers all have their own manual checks for whether they've been fitted before calling their main methods. I'd like to refactor this to use a common decorator pattern.

Additionally, I've found that when I train a CRFSlotFiller on an intent that doesn't have any slots (just plain text utterances), it still tries to train a CRF model which is wasteful. The slot filler should handle this case gracefully by skipping CRF training entirely and returning empty results when asked to extract slots.

Can you help me implement a `fitted_required` decorator and update the CRFSlotFiller to properly handle intents without slots?
