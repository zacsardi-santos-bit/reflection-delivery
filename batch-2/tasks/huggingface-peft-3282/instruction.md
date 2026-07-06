I'm working with a model that has several fine-tuning adapters loaded at the same time, and I'm running into a bug when I try to save just one of them.

*   When saving a model with multiple adapters and specifying one adapter by name, the resulting checkpoint file must contain exactly the number of weights that belong to that specific adapter — no weights from other adapters should be included.

*   The adapter name selection during save must use exact string matching. An adapter named 'default' must not cause weights from adapters named 'default2' (target is prefix), 'other_default' (target is suffix), 'foodefault_bar' (target is infix), or 'efaul' (a substring of target) to be included in the saved output.

*   The saved file must contain exactly as many weight entries as are returned by retrieving the PEFT model state dict for the selected adapter alone.

*   The fix must apply across all supported adapter types (e.g., LoRA, IA3, BOFT, OFT, HRA, VeRA, LoHa, LoKr, LNTuning, and others) when saving with an explicit adapter name selection.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.