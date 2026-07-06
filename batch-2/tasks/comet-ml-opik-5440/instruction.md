I need a reusable frontend component that automatically turns URLs embedded in text into clickable links.

*   The LinkifyText component must detect URLs starting with http:// or https:// within its children and render each as a clickable <a> anchor element whose href matches the URL and whose text content matches the URL.

*   Only http:// and https:// protocols must be linkified. URLs using ftp://, mailto:, file://, javascript:, data:, or any other scheme (including no scheme at all, e.g. example.com/path) must NOT produce anchor elements.

*   Misspelled or non-standard protocol prefixes (e.g. htp://) must NOT be linkified.

*   Every generated anchor element must have target='_blank' and rel='noopener noreferrer'.

*   Every generated anchor element must have a className that includes the classes 'text-blue-600', 'underline', and 'break-all'.

*   Every generated anchor element must have a click handler that stops event propagation, preventing click events from reaching parent elements.

*   When multiple URLs appear in one string (separated by text or newlines), each URL must produce a separate anchor element.

*   URLs that appear adjacent to punctuation (trailing period, trailing comma, enclosing parentheses, enclosing brackets) must be linkified with the punctuation characters excluded from the URL — the href must point to the URL only.

*   The component must be XSS-safe: script tags present in children must not be injected into the DOM as script elements, and javascript: and data: URI schemes must never produce anchor elements.

*   When the children is a plain string that does not contain 'http' or 'https', the component must render the text directly without any extra wrapper element — the container's innerHTML must equal the plain text string exactly.

*   The component must gracefully handle null, undefined, and numeric children: null and undefined children must render as empty content (no links, empty text), and a numeric child must render its string representation without any link elements.


*   Interface details: Type: Component
Name: LinkifyText
Location: apps/opik-frontend/src/components/shared/LinkifyText/LinkifyText.tsx
Description: A React component that scans its children for http:// and https:// URLs and renders them as clickable anchor elements. The component is the default export of the file.
Signature: LinkifyText({ children }: { children?: React.ReactNode }) -> JSX.Element

The component must:
- Accept children of type string, number, null, undefined, or any React node
- Return anchor elements for detected http/https URLs with the following attributes:
  - target="_blank"
  - rel="noopener noreferrer"
  - CSS className including "text-blue-600", "underline", and "break-all"
  - onClick handler that calls event.stopPropagation()
- For string children that do not contain "http" or "https", render the string directly as the sole content of the container (no wrapper element), so that container.innerHTML equals the plain text string exactly


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.