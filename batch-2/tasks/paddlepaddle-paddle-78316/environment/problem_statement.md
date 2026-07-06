## Description

When computing CTC loss using padded sequences — where the caller provides explicit per-sequence length tensors for both the input log-probabilities and the labels — there is currently no validation that the batch dimensions are consistent across all inputs. This means that if a labels tensor, an input-length tensor, or a label-length tensor has a different number of sequences than the log-probabilities tensor, the computation silently proceeds rather than reporting an error.

## Expected Behavior

- If the labels tensor has a different batch size than the log-probabilities, the operation should raise an error clearly stating which input is mismatched, what size was expected, and what size was actually provided.
- The same validation should apply to the per-sequence input-length tensor and the per-sequence label-length tensor — each must match the batch size derived from the log-probabilities tensor.
- The error messages should be descriptive enough for users to immediately identify which input is wrong and what the correct size should be.

## Why This Matters

Without this validation, mismatched batch dimensions can lead to silent incorrect results or cryptic low-level errors that are hard to debug. Users who accidentally pass a labels or length tensor with the wrong number of sequences should receive a clear, actionable error message identifying the source of the problem.
