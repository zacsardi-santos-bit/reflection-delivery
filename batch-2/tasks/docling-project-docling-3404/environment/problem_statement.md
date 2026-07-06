## Description

The document conversion pipeline currently cannot process outputs from two recently released AI OCR model families. One model family produces structured HTML where each layout element is annotated with a bounding box and a semantic label; the other produces a JSON array describing the bounding box, category, and text content of every detected layout element. Neither format has a parser in the codebase, so documents converted with these models cannot be turned into the pipeline's standard document representation.

In addition, a general-purpose utility for computing the image dimensions that these visual models expect as input is needed. The utility must round dimensions to a required factor, apply pixel-budget constraints (minimum and maximum total pixels), handle per-dimension size clamping, and respect a user-supplied scale factor. A second utility to strip model-specific end-of-sequence markers from decoded model outputs is also needed, so that decoded text does not carry trailing stop tokens into the rest of the pipeline.

## Expected Behavior

- A parser for the HTML-based format must convert each annotated block into the correct document element type (text, table, figure, section header, caption, footnote, page header/footer, list items, etc.) and correctly scale the bounding-box coordinates from the model's normalized coordinate space to the actual page dimensions.
- A parser for the JSON-based format must do the same, handling the model's coordinate space via an optional reference image size for rescaling, and must gracefully recover from truncated or malformed output that is common in model-generated JSON.
- The image-size utility must produce dimensions that are multiples of a configurable factor and satisfy pixel-budget bounds (minimum and maximum).
- The stop-string stripping utility must remove the first occurrence of any recognized stop token from decoded outputs, leaving partial trailing prefixes intact.

## Why This Matters

Without these parsers, developers cannot use either of these model families as a drop-in OCR backend. The JSON-based models are also particularly prone to producing truncated output, so robust recovery from malformed JSON is essential for reliable operation.
