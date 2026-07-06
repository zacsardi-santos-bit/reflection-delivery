I've noticed a bug in mitmweb's content viewer where blank lines in HTTP message bodies don't display correctly.

*   The ContentRenderer component must render each line of content inside a div element; when a line is empty or blank, the div must contain a space character rather than being empty/self-closing

*   An empty line rendered by ContentRenderer must produce an opening and closing div tag with a space character inside (not a self-closing empty tag), so that blank lines are visually preserved in the output

*   The rendered HTML for an empty line must match: an explicit opening div, a space character as content, and a closing div — this ensures multiple consecutive blank lines each appear as distinct visible elements in the content view


*   Interface details: Type: Component
Name: ContentRenderer
Location: web/src/js/components/contentviews/ContentRenderer.tsx
Description: React memoized component that renders lines of text content inside a <pre> element. Each content line is rendered in its own <div>. For empty/blank lines, the <div> must contain a space character (" ") rather than being empty, so it renders as an explicit open/close tag pair with whitespace content instead of a self-closing element.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.