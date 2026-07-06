## Description

When using the new frontend pipeline for model conversion, there is no way to inspect the structure of a model — specifically the names, shapes, and data types of its inputs and intermediate tensors — without running the full conversion. Tooling that needs to analyze a model before conversion has no structured output to consume.

We need a model analysis mode that, when triggered via an environment variable, outputs a structured description of all input parameters and intermediate tensors in the model's graph. This analysis should run early in the pipeline, before any user-specified transformations are applied, and should halt further processing after outputting the analysis.

## Expected Behavior

- When the analysis mode is activated through an environment variable, the pipeline should analyze the model's structure and produce a structured JSON-compatible description.
- The description must include all input parameters with their names, shapes, and data types.
- The description must also include all intermediate tensors (outputs of every operation in the graph), also with their names, shapes, and data types.
- Shapes should handle dynamic dimensions gracefully: static dimensions are reported as integers, dynamic dimensions within a static-ranked shape are reported as zero, and tensors with fully dynamic rank report their shape as a special sentinel value.
- Data types should be reported as their string representation when known, or as a sentinel value when not determinable.
- A placeholder value field should be included for every tensor (currently unsupported, always reports a sentinel value).
- After producing the analysis output, the pipeline should exit without continuing the conversion.

## Why This Matters

Tools and integrations built on top of the model conversion pipeline need a way to query a model's structure programmatically. Without this analysis mode, users have no way to discover input/output tensor names, shapes, and data types before attempting a full conversion.
