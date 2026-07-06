## Description

The streaming reasoning parser for MiniMax M3 models fails to correctly detect reasoning block boundaries when the delimiter tokens are split into multiple smaller tokens rather than appearing as a single atomic vocabulary token. This can happen when the model's tokenizer encodes the special marker strings as character-level or subword tokens at runtime, even if the markers exist as single vocabulary entries.

The parser was originally written to match boundaries by checking for a single specific token ID. When the tokenizer produces the same marker text as a sequence of multiple tokens instead, the parser does not recognize that a reasoning block has started or ended. This results in incorrect streaming output — reasoning content leaking into the content field, end states not being set at the right time, or content being misidentified as reasoning.

## Expected Behavior

- The parser must detect start and end markers using the full accumulated text rather than by matching a single token ID.
- When a marker string is split across multiple streaming chunks, the parser should correctly hold off on emitting reasoning or content until the marker is complete.
- After the end marker is fully received, the end state should be set and subsequent content should be attributed correctly.
- Helper methods that inspect token sequences (checking whether reasoning has ended, counting reasoning tokens, extracting content tokens) must also use multi-token sequence matching rather than single-token lookups.
- A leading end marker that is split across chunks should still result in the correct behavior: no reasoning output, with the following text treated as content.

## Why This Matters

Models and tokenization configurations may vary, and assuming markers always map to a single token is brittle. Fixing the parser to work from text-level detection ensures that reasoning/content separation is correct across all tokenization scenarios.
