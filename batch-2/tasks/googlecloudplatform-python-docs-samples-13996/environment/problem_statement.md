## Description

The Google Cloud Retail Search API supports several useful search patterns that developers commonly need — including offset-based result navigation, manual page-by-page pagination using page tokens, and category-based browsing as an alternative to keyword search. Currently, there are no sample scripts demonstrating these capabilities, which makes it harder for developers to understand how to integrate them into their own applications.

## Expected Behavior

- A sample that demonstrates how to search with an offset so results start from a specific position in the result set, printing the offset used and the product IDs found.
- A sample that demonstrates manual pagination by fetching the first page of results and then using the page token from that response to fetch a second page, clearly labeling each page in the output.
- A sample that demonstrates the difference between text-based searching (using a query string) and category-based browsing (using a list of page categories), printing product IDs, titles, and relevance scores for each result.
- The request-based sample should also handle API errors gracefully, printing a useful error message to stderr that includes the project ID.

## Why This Matters

Without these samples, developers have to discover on their own how to use offset navigation, token-based pagination, and category browsing with the Retail Search API. Providing ready-to-run examples reduces onboarding time and reduces the risk of incorrect API usage.
