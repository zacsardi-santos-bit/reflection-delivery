I'm building out the retail search sample library for Google Cloud and need to add three new snippet files covering common search patterns that are currently missing.

*   The search_offset function must accept project_id, placement_id, visitor_id, query, and offset as parameters, submit a search request with the given offset, page_size of 10, and query, then print '--- Results for offset: <offset> ---' followed by 'Product ID: <id>' for each result to stdout.

*   The search_pagination function must accept project_id, placement_id, visitor_id, and query as parameters, make exactly two search calls each with page_size of 5, use no page_token on the first call, use the next_page_token from the first response as the page_token on the second call, and print '--- First Page ---' with 'Product ID: <id>' for the first page and '--- Second Page ---' with 'Product ID: <id>' for the second page to stdout.

*   The search_request function must accept project_id, placement_id, visitor_id, and optionally query and page_categories as parameters; when query is provided, it must be set in the request and page_categories must be empty; when page_categories is provided, it must be set in the request and query must be empty.

*   The search_request function must print 'Product ID: <id>', 'Title: <title>', and 'Scores: <model_scores dict>' for each result to stdout.

*   The search_request function must catch google.api_core.exceptions.InvalidArgument errors and print 'error: <message>' and 'Project: <project_id>' to stderr.


*   Interface details: Type: Function
Name: search_offset
Location: retail/snippets/search_offset.py
Signature: search_offset(project_id, placement_id, visitor_id, query, offset)
Description: Performs a retail search with the given offset. Submits a search request with offset, page_size=10, and query set. Prints "--- Results for offset: <offset> ---" to stdout, then "Product ID: <id>" for each result.

Type: Function
Name: search_pagination
Location: retail/snippets/search_pagination.py
Signature: search_pagination(project_id, placement_id, visitor_id, query)
Description: Demonstrates manual pagination by making two search calls, each with page_size=5. The first call has no page_token; the second call uses the next_page_token from the first response. Prints "--- First Page ---" and "Product ID: <id>" for each result on the first page, then "--- Second Page ---" and "Product ID: <id>" for each result on the second page.

Type: Function
Name: search_request
Location: retail/snippets/search_request.py
Signature: search_request(project_id, placement_id, visitor_id, query=None, page_categories=None)
Description: Performs a retail search in either text-search or category-browse mode. When query is provided it is set in the request and page_categories is left empty; when page_categories is provided it is set in the request and query is left empty. Prints "Product ID: <id>", "Title: <title>", and "Scores: <model_scores>" for each result to stdout. Catches google.api_core.exceptions.InvalidArgument and prints "error: <message>" and "Project: <project_id>" to stderr.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.