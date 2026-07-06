Implement a document compressor that integrates with an external prompt compression tool to reduce the size of retrieved documents before they are passed to a language model. Ensure that the compressor maintains the original metadata of each document and handles empty document lists gracefully.

*   Implement the `LLMLinguaCompressor` class in `libs/community/langchain_community/document_compressors/llmlingua_filter.py`.
    *   Ensure it is importable from `langchain_community.document_compressors`.
    *   Accept an `instruction` parameter in its constructor.
    *   Use `llmlingua.PromptCompressor` internally for compression.

*   Implement the `_format_context(docs)` static method.
    *   Accept a list of `Document` objects.
    *   Return a list of strings, formatting each document's `page_content` with reference tags: `"\n\n<#refN#> {content} <#refN#>\n\n"` where N is the 0-based index.

*   Implement the `extract_ref_id_tuples_and_clean(contents)` instance method.
    *   Accept a list of strings.
    *   Return a list of tuples `(cleaned_content, ref_id)`.
        *   For strings with `<#refN#>` tags, extract the inner content and return with the integer ref ID N.
        *   For strings without reference tags, return `(original_content, -1)`.
        *   Return an empty list for an empty input list.

*   Implement the `compress_documents(documents, query, callbacks=None)` method.
    *   Return an empty list when given an empty `documents` list.
    *   Use the underlying prompt compressor to compress the provided documents.
    *   Return a list of `Document` objects with compressed `page_content` and original metadata preserved.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.