## Description

The tool-call parsing system currently does not recognize Tencent's Hy3 family of models. When a user specifies a Tencent Hy3 model and tool-calling is enabled, the system fails to automatically select the appropriate parser because no pattern mapping exists for this model family.

## Expected Behavior

- The system should automatically identify Tencent Hy3 models by their model name and route them to the appropriate tool parser.
- A named entry for this model family should exist in the parser registry so that it can be referenced by name, similar to how other currently supported model families are handled.

## Why This Matters

Without this support, users who want to use Tencent Hy3 models with tool-calling capabilities cannot rely on automatic parser selection. They would have to manually configure the parser or go without tool-calling support entirely. Adding automatic recognition brings Hy3 in line with the other supported models in the system.
