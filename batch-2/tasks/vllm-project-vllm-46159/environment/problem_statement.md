## Description

When a language model produces a reasoning/thinking section before its actual response, and that response begins with multi-byte characters (e.g. Korean, Chinese, Japanese), the streamed output becomes corrupted at the transition point. The first few characters of the content are replaced by the Unicode replacement character instead of the intended text.

## Root Cause

This happens because the byte sequences encoding multi-byte characters can be split across the reasoning/content boundary. When the content-side fragment is decoded in isolation (rather than together with the preceding byte context), the tokenizer cannot reconstruct the original character and falls back to the replacement character. The fix should flush the reasoning parser's accumulated byte context at the transition, ensuring multi-byte characters that straddle the boundary are decoded correctly.

## Expected Behavior

- After the reasoning section ends and content begins, multi-byte characters in the content output must appear correctly decoded.
- The Unicode replacement character must never appear in the content output, regardless of how the token stream is chunked during streaming.
- This must work correctly for all relevant model configurations (including GLM4.7-MoE and Qwen3 families).
- Correct behavior must hold across all streaming chunk sizes (individual tokens, small batches, and full sequences).

## Why This Matters

Users relying on streaming output from models that use structured reasoning (think-then-respond) would observe garbled characters at the start of responses whenever the content begins in a non-Latin script. This is a regression that silently corrupts model output without raising errors.
