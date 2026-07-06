## Description

The QuiverAI provider needs significant expansion to support image vectorization in addition to text-to-SVG generation, along with several quality-of-life improvements for the existing generation workflow.

## Missing Capabilities

### Vectorization Mode
Currently the QuiverAI provider only supports generating SVG graphics from text prompts. We need to add a second operating mode for converting raster images (PNG, JPEG, etc.) into SVG vector graphics. Users should be able to configure a provider in this vectorization mode and supply image input through the test prompt in multiple flexible formats:
- A plain image URL
- An inline base-64-encoded data URL
- A JSON object describing the image reference
- A raw base-64 string

Invalid or unsupported input formats should return descriptive errors before any API call is made.

### Updated Default Model
The previous default model is being retired. The default should be updated to the current recommended model.

### Metadata Exposure
Currently the provider does not surface billing credits or response identifiers in its output. Both should be exposed in the response metadata so callers can track usage and trace individual requests.

### Reference Input Normalization
The generation endpoint accepts reference images but currently requires them to be provided as structured objects. Simple URL strings in the references list should be automatically normalized to the expected format.

### Weekly Quota Handling
Rate-limit responses indicating that a weekly usage quota has been exhausted are currently retried, which wastes time since retries cannot recover from a hard quota limit. These should be surfaced as errors immediately.

### Streaming Multi-Output Ordering
When requesting multiple SVG outputs via streaming, the outputs may arrive out of order. They should be reordered by their assigned position index rather than arrival order.

## SVG Media Library Indexing

When a provider returns SVG markup as plain text output, that SVG should be automatically indexed in the media library so it can be rendered visually in the interface. However, the original SVG text must remain as the output value so that text-based assertions and AI judges can still read and evaluate the actual content—not an internal storage reference. If the SVG has already been indexed (detected via content hash), no duplicate storage operation should occur. Content with multiple root SVG documents should not be indexed as a single entry.

## Expected Behavior

- A vectorization mode is available alongside generation, routing to the correct API endpoint
- The vectorization mode's provider identifier and display name distinguish it from generation
- Various image input formats are accepted and validated with informative error messages
- Response metadata includes credits consumed and the response identifier
- String references are accepted in the generation config and normalized automatically
- Weekly quota errors are not retried
- Multi-output streaming results respect index ordering
- Single-SVG text output is indexed in the media library while the text output is preserved for judges and assertions
