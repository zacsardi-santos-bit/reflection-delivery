Implement a utility module in the agent directory that handles conversions between the AI model's native parts format and the internal content representation. Ensure consistent handling of edge cases and provide a centralized solution for these conversions.

*   Implement the `geminiPartsToContentParts` function in `packages/core/src/agent/content-utils.ts`:
    *   Convert text parts to `ContentPart` objects with type 'text'.
    *   Convert thought parts to `ContentPart` objects with type 'thought', including `thoughtSignature` if present.
    *   Convert inlineData parts to `ContentPart` objects with type 'media', preserving `data` and `mimeType`.
    *   Convert fileData parts to `ContentPart` objects with type 'media', mapping `fileUri` to 'uri' and preserving `mimeType`.
    *   Skip functionCall and functionResponse parts.
    *   Serialize unknown part types to `ContentPart` with type 'text', JSON-stringified part as text, and `_meta` field marking it as unknown.

*   Implement the `contentPartsToGeminiParts` function in `packages/core/src/agent/content-utils.ts`:
    *   Convert 'text' `ContentParts` to Gemini parts with a text field.
    *   Convert 'thought' `ContentParts` to Gemini parts with text, `thought: true`, and optional `thoughtSignature`.
    *   Convert 'media' `ContentParts` with `data` to inlineData parts, defaulting `mimeType` to 'application/octet-stream' if missing.
    *   Convert 'media' `ContentParts` with `uri` to fileData parts.
    *   Skip 'media' `ContentParts` with neither `data` nor `uri`.
    *   Serialize unknown `ContentPart` variants to a Gemini Part with JSON-stringified content.

*   Implement the `toolResultDisplayToContentParts` function in `packages/core/src/agent/content-utils.ts`:
    *   Return `undefined` for `undefined` or `null` inputs.
    *   Return `[{ type: 'text', text: value }]` for string inputs.
    *   Return `[{ type: 'text', text: JSON.stringify(value) }]` for object inputs.

*   Implement the `buildToolResponseData` function in `packages/core/src/agent/content-utils.ts`:
    *   Return `undefined` when no recognized fields are set in the input.
    *   Include `errorType`, `outputFile`, and `contentLength` fields in the output if present.
    *   Merge properties of `data` object into the top-level result when provided.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.