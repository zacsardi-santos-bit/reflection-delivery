## Description

The rapid OCR integration currently ignores the language setting when selecting model assets. Even when a user explicitly requests English-language OCR processing, the system silently falls back to Chinese model weights for both detection and recognition. This means users working on English documents get worse OCR quality because the wrong models are being used.

## Expected Behavior

- When English is selected as the OCR language, the model should load English-optimized detection and recognition assets, not Chinese ones.
- When no language is specified, Chinese should remain the default.
- The model download utility should fetch models for both English and Chinese, covering all supported execution backends, so offline deployments have all needed assets available without manual intervention.

## Why This Matters

Users processing English-language documents are currently getting suboptimal results because their language preference is not respected. This is especially problematic since the option to specify a language already exists in the interface — users reasonably expect it to do something. Fixing language-aware model selection would immediately improve OCR quality for English documents without requiring any changes from users beyond what they're already doing.
