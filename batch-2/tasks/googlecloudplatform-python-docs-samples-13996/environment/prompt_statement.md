I'm building out the retail search sample library for Google Cloud and need three new snippet files covering common search patterns we're missing right now, since developers currently have to figure out offset navigation, token-based pagination, and category browsing on their own with the Retail Search API and I'd rather give them ready-to-run examples.

First one should demo how to search with an offset so results start from a specific position in the result set. It needs to print a header showing the offset value used and then list the product IDs found.

Second should show manual pagination through results using page tokens. So it fetches the first page, pulls the token out of that response, uses it to fetch the second page, and prints clearly labeled output for each page (like page one vs page two) with the product IDs found on each.

Third one handles two distinct modes: a keyword-based text search and a category-based browsing search. When a query string is provided, use it for the search and leave the category list empty. When a list of page categories is provided instead, use those for browsing and leave the query empty. For each result print the product ID, title, and the relevance scores. Oh and this request-based one should also handle API errors gracefully, printing a useful error message plus the project ID to stderr.

That's the three, offset search, token pagination, and text-vs-category browsing, each as its own runnable sample.
