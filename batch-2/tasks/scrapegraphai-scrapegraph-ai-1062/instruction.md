I'm working on ScrapeGraphAI and want to add a new document loader that uses a lightweight external tool to fetch web pages, as an alternative to the existing Chromium-based loader.

*   PlasmateLoader must accept a list of URLs as the first positional argument, plus keyword-only parameters: output_format (default 'text'), timeout in seconds (default 30), selector (default None), extra_headers (default empty dict when None is passed), and fallback_to_chrome (default False). All parameters must be stored as instance attributes with the same names.

*   PlasmateLoader must raise a ValueError with a message containing 'output_format' when initialized with an unsupported output format value (e.g. 'html' is not valid; 'text', 'som', and 'markdown' are valid).

*   The _build_cmd method must return a list where the first element contains the string 'plasmate', and the list also contains 'fetch', the URL, '--format', the current output_format, '--timeout', and the timeout value multiplied by 1000 and converted to a string (so a 30-second timeout becomes '30000').

*   When a selector is set, _build_cmd must include '--selector' in the returned command list, with the selector value immediately following it.

*   When extra_headers are set, _build_cmd must include '--header' in the returned command list, with the header formatted as 'key: value' immediately following it.

*   lazy_load must yield Document objects; each document must have page_content set to the fetched text and metadata containing: 'source' set to the URL string, 'loader' set to the literal string 'plasmate', and 'format' set to the current output_format string.

*   lazy_load must process multiple URLs and yield one Document per URL that returns non-empty content.

*   lazy_load must skip a URL and yield no document when the fetched content is empty, and must emit a log warning containing the text 'Empty content'.

*   lazy_load must skip a URL and yield no document when the subprocess exits with a non-zero return code.

*   lazy_load must skip a URL and yield no document when the fetch times out, and must emit a log warning containing the text 'Timeout'.

*   lazy_load must raise an ImportError with a message matching 'plasmate is required' when the plasmate binary cannot be found on the system.

*   lazy_load must yield nothing (return an empty iterator) when given an empty URL list.

*   alazy_load must be an async generator that yields Document objects with the same metadata structure as lazy_load ('source', 'loader', 'format') and processes URLs concurrently.

*   alazy_load must skip URLs that return empty content and yield no document for them.

*   alazy_load must yield nothing when given an empty URL list.


*   Interface details: Type: Class
Name: PlasmateLoader
Location: scrapegraphai/docloaders/plasmate.py
Description: A document loader that fetches web pages using an external lightweight tool and yields Document objects. Must extend BaseLoader from langchain_community.
Signature:
  __init__(urls: List[str], *, output_format: str = "text", timeout: int = 30, selector: Optional[str] = None, extra_headers: Optional[dict] = None, fallback_to_chrome: bool = False, **chrome_kwargs) -> None
  _build_cmd(url: str) -> List[str]
  lazy_load() -> Iterator[Document]
  alazy_load() -> AsyncIterator[Document]

Notes on __init__:
- Stores urls, output_format, timeout, selector, extra_headers (defaults to {} if None), fallback_to_chrome as instance attributes
- Raises ValueError with a message containing "output_format" for any format not in the valid set (e.g. "html" is invalid; "text", "som", "markdown" are valid)

Notes on _build_cmd:
- Returns a list where cmd[0] contains the string "plasmate"
- List includes "fetch", the URL, "--format", the format string, "--timeout", and the timeout converted to milliseconds (timeout * 1000) as a string (e.g., 30 seconds → "30000")
- If selector is set, appends "--selector" followed by the selector string
- For each key/value in extra_headers, appends "--header" followed by "key: value" formatted string

Notes on lazy_load:
- Yields Document objects with page_content set to fetched content and metadata containing: "source" (the URL), "loader" (literal string "plasmate"), "format" (the output_format)
- If content is empty, skips the URL and logs a warning message containing the text "Empty content"
- If the subprocess exits with a non-zero return code, skips the URL
- If subprocess times out, skips the URL and logs a warning message containing the text "Timeout"
- If the plasmate binary is not found on the system, raises ImportError with a message matching "plasmate is required"
- If urls list is empty, yields nothing

Notes on alazy_load:
- Same Document structure and metadata fields as lazy_load
- Skips URLs that return empty content
- If urls list is empty, yields nothing


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.