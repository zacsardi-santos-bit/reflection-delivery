Implement a robust parsing mechanism in the `MiniMaxM3ReasoningParser` class to accurately detect reasoning block boundaries using text-level detection of delimiter strings, regardless of how the tokenizer splits these markers. Update the class methods to handle multi-token sequences and ensure correct attribution of reasoning and content.

*   Update `is_reasoning_end(input_ids: Sequence[int]) -> bool`:
    *   Return `True` when a completed reasoning block is detected in the token ID sequence.
    *   Return `False` if only a start marker is present or if no markers are present.

*   Update `extract_content_ids(input_ids: list[int]) -> list[int]`:
    *   Locate the end marker as a token sequence.
    *   Return tokens after the end marker when reasoning has ended.
    *   Return an empty list when only the start marker is present.
    *   Return the input IDs unchanged for plain content with no markers.

*   Update `count_reasoning_tokens(token_ids: Sequence[int]) -> int`:
    *   Use multi-token sequence matching for start and end markers.
    *   Count tokens inside reasoning blocks correctly.

*   Update `is_reasoning_end_streaming(input_ids: Sequence[int], delta_ids: Iterable[int]) -> bool`:
    *   Detect the end of reasoning based on text-level marker detection.
    *   Return `True` after the end marker string has been fully seen across accumulated streaming deltas.

*   Update `extract_reasoning_streaming(...)`:
    *   Correctly attribute reasoning and content when markers are split across multiple streaming chunks.
    *   Ensure the end state is not set until the complete end marker string has been accumulated.
    *   Handle leading end markers split across chunks, returning `reasoning=None` and treating subsequent text as content with appropriate end states.

*   Ensure `extract_reasoning_streaming` in 'thinking_mode=enabled':
    *   Correctly handle split end markers, returning reasoning content and setting the end state upon completion of the end marker text.

*   When processing a delta containing a split end marker followed by content:
    *   Ensure a subsequent call to `extract_content_ids` with the same delta token IDs returns token IDs that decode to only the content text.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.