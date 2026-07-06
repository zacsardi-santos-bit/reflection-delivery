## Description

Xinference currently supports the Qwen LLM model family only in PyTorch format. However, users who want to run Qwen models with reduced memory footprint and faster inference need GGML format support. The qwen.cpp project (https://github.com/QwenLM/qwen.cpp) provides C++ bindings for running quantized Qwen models, but Xinference does not have integration for this backend.

## Expected Behavior

- Users should be able to load and run Qwen models in GGML format (ggmlv3) using the qwen_cpp library
- The QWenModel class should support both chat and text generation modes
- Chat mode should return properly formatted ChatCompletion responses (or streaming ChatCompletionChunk responses)
- Generate mode should return properly formatted Completion responses (or streaming CompletionChunk responses)
- The model should be downloadable from HuggingFace or ModelScope with the associated qwen.tiktoken file
- The model should handle generate_config parameters like max_tokens, top_p, temperature, and stream

## Current Behavior

Currently, there is no QWenModel class for GGML format support. When users try to load Qwen models in GGML format, the system either falls back to LlamaCpp (which is not compatible) or fails to find a matching model class.
