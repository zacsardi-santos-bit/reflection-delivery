Implement a module to correctly infer and assign hierarchical levels to headings in structured documents based on their numbering schemes. Ensure that the output reflects the document's structure in formats like Markdown, with configurable options for handling numbering precedence and maximum depth.

*   Create a new module at `docling/models/stages/heading_hierarchy/heading_hierarchy_model.py` exporting:
    *   `HeadingHierarchyModel`
    *   `_infer_from_numbering`
    *   `_parse_marker`

*   Define `HeadingHierarchyOptions` in `docling/datamodel/pipeline_options.py` with fields:
    *   `enabled`: bool
    *   `use_style`: bool
    *   `use_numbering`: bool
    *   `max_level`: int
    *   `numbering_schemes`: list of str

*   Implement `_parse_marker(text: str) -> object | None`:
    *   Return an object with a `.family` attribute for recognized numbering markers.
    *   Recognized families: 'roman_u', 'alpha_u', 'part', 'article'.
    *   Return `None` for plain text headings.

*   Implement `_infer_from_numbering(headings: list, options: HeadingHierarchyOptions) -> dict[int, int]`:
    *   Accept a list of objects with a `.text` attribute and a `HeadingHierarchyOptions` instance.
    *   Return a dict mapping each heading index to its inferred level.
    *   Omit headings without a recognized numbering marker.
    *   Assign different levels to headings from different numbering schemes.
    *   Handle legal-style nested numbering across five schemes: PART, single-decimal, double-decimal, parenthetical-letter, and parenthetical-roman.
    *   Produce levels relative to the schemes present in the document.
    *   Respect custom `numbering_schemes` order when provided.

*   Implement `HeadingHierarchyModel`:
    *   `__init__(self, options: HeadingHierarchyOptions) -> None`
    *   `assign_heading_levels(self, doc: DoclingDocument, parsed_pages: dict | None = None) -> DoclingDocument`:
        *   Set the `.level` attribute on each `SectionHeaderItem` in `doc.texts`.
        *   Clamp levels to `max_level` if set.
        *   Use `_infer_from_numbering` when `use_numbering=True`.
        *   Use bounding box height when `use_numbering=False` and `use_style=True`.

*   Ensure Markdown export reflects the assigned depth:
    *   Level 1 headings export as '# heading text'.
    *   Level 2 headings export as '## heading text'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.