I'm working on improving the accessibility of HTML documents exported from our editor.

*   The improveHtmlAccessibility function must convert elements with data-content-type="heading" and a data-level attribute into the corresponding semantic heading element (h1–h6), where the numeric data-level value determines the heading level (clamped to 1–6).

*   When a heading block already contains an inner heading element at the same level as data-level, the block must be replaced by a single heading element at that level (no nesting). The inner heading's class name must be copied to the output heading element.

*   When a heading block contains an inner heading element whose tag level differs from the data-level attribute, the output must use the level from data-level (the outer attribute takes precedence), and the mismatched inner heading must be removed.

*   When no h1 element exists in the document after processing heading blocks, improveHtmlAccessibility must insert an h1 element with id="doc-title" and text content equal to the provided title string.

*   When the document already contains an h1 element after processing, no additional h1 must be inserted.

*   Elements with data-content-type="bulletListItem" inside block-outer wrappers must be converted into li elements nested inside a ul element.

*   Elements with data-content-type="numberedListItem" inside block-outer wrappers must be converted into li elements nested inside an ol element.

*   When a non-list block (such as a heading) appears between list items of the same type within the same block group, the list items must be split into separate list elements with the intervening block appearing between them in the output HTML. The first list must appear before the heading, and the second list must appear after it.

*   Consecutive same-type list items with no intervening non-list block must be grouped into a single list element (one ul or one ol).

*   Elements with data-content-type="quote" must be converted to blockquote elements containing the original content.

*   Elements with data-content-type="callout" must be converted to aside elements with the attribute role="note".

*   Elements with data-content-type="checkListItem" must be wrapped in a ul element with class="checklist" and role="list". Checkbox inputs within checklist items must have the aria-checked attribute set to "false" when unchecked.

*   Elements with data-content-type="codeBlock" must be converted to pre elements. The original class name and data-language attribute from the source element must be preserved on the pre element. The text content must be wrapped in a code child element.

*   Images that do not have an alt attribute must have alt="" added. Images that already have an alt attribute must not have it modified.

*   The entire body content must be wrapped in an article element with role="document" and aria-labelledby="doc-title".


*   Interface details: Type: Function
Name: improveHtmlAccessibility
Location: src/frontend/apps/impress/src/features/docs/doc-export/utils_html.ts
Signature: improveHtmlAccessibility(parsedDocument: Document, title: string): void
Description: Transforms a parsed HTML document in-place so that non-semantic editor markup is replaced with proper semantic HTML elements, improving screen-reader accessibility. The first argument is the Document object to mutate; the second is the document title string used as a fallback h1 when none exists.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.