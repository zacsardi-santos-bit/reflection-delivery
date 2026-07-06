## Description

The vllm Rust chat layer automatically routes model identifiers to the correct tool-call parser so users do not have to configure this manually. However, there is currently no routing entry for the InternLM2 family of models. Any user running an InternLM2 or InternLM2.5 model is left without automatic tool-call parsing support.

## Expected Behavior

- Models whose identifier includes the InternLM2 version marker (both the dashed form and the underscored form, e.g., internlm2-chat-7b or internlm2_5-7b-chat) should be automatically routed to the dedicated InternLM2 tool-call parser.
- Older InternLM v1 models (whose identifier does not contain the InternLM2 version marker) should continue to route to the Llama-based parser and should **not** be picked up by the InternLM2 routing rule.
- InternLM v3 models, which also use the Llama architecture, should likewise be unaffected.
- The Intern-S1 and Intern-S1-Pro model lines, which have their own separate parser, must not be captured by the InternLM2 routing rule.

## Why This Matters

InternLM2 uses a prompt format with special tokens that differs from both InternLM v1 and the Llama architecture. Without a dedicated routing rule, InternLM2 users cannot use the automatic tool-calling feature at all. Adding this rule aligns the Rust implementation with the existing Python-side support and lets InternLM2 users benefit from streaming tool-call parsing with no manual override needed.
