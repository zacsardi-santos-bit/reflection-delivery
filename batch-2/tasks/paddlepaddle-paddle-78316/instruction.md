I'm using the CTC loss function in padded sequence mode, where I pass explicit length tensors alongside the log-probabilities and labels.

*   When paddle.nn.functional.ctc_loss is called in padded mode (with explicit input_lengths and label_lengths tensors), it must validate that the labels tensor has the same batch size (dimension 0) as the log_probs tensor's batch dimension (dimension 1). If they differ, a ValueError must be raised with a message matching the pattern: "Expected label to have size {N} at dimension 0, but got size {M}", where N is the expected batch size from log_probs and M is the actual size of labels at dimension 0.

*   When paddle.nn.functional.ctc_loss is called in padded mode, it must validate that the input_lengths tensor has the same batch size (dimension 0) as the log_probs tensor's batch dimension (dimension 1). If they differ, a ValueError must be raised with a message matching the pattern: "Expected logits_length to have size {N} at dimension 0, but got size {M}", where N is the expected batch size and M is the actual size of input_lengths at dimension 0.

*   When paddle.nn.functional.ctc_loss is called in padded mode, it must validate that the label_lengths tensor has the same batch size (dimension 0) as the log_probs tensor's batch dimension (dimension 1). If they differ, a ValueError must be raised with a message matching the pattern: "Expected labels_length to have size {N} at dimension 0, but got size {M}", where N is the expected batch size and M is the actual size of label_lengths at dimension 0.

*   The batch size validation is triggered only when input_lengths and label_lengths are provided (padded mode). The log_probs tensor shape is [T, N, C] where N is the batch size at dimension 1; labels, input_lengths, and label_lengths must all have N as their dimension 0 size.

*   The validation must handle the edge case where any of labels, input_lengths, or label_lengths has a batch size of 0 (empty tensor at dimension 0) when log_probs has a non-zero batch size — this must raise the appropriate ValueError.


*   Interface details: NO INTERFACES NEEDED

The change adds batch size validation to the existing CTC loss infrastructure. No new Python-level functions, classes, or methods are introduced. The validation is added inside the existing WarpCTC shape inference implementation at `paddle/phi/infermeta/multiary.cc` within the `WarpctcInferMeta` function. The Python-facing API `paddle.nn.functional.ctc_loss` is unchanged in signature; only its runtime validation behavior is extended.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.