## Description

When building retrieval-augmented generation (RAG) pipelines, retrieved documents can be extremely long and token-heavy, which increases cost and can exceed context window limits. There is currently no standard integration in this library to compress retrieved documents using an external prompt compression tool before passing them to an LLM.

## Expected Behavior

- A new document compressor integration should be available that leverages an external prompt compression library to reduce the size of retrieved document content
- The compressor should accept an instruction string that guides the compression process
- When compressing documents, the original metadata (such as source, ID, etc.) of each document must be preserved in the output
- If no documents are provided, the compressor should return an empty list without error
- The compressor should be accessible from the standard document compressors module

## Why This Matters

Without this integration, developers cannot easily reduce the token count of retrieved context, which can cause failures or degraded performance when working with long documents. This addition allows the retrieval pipeline to intelligently compress documents while retaining their traceability through preserved metadata.
