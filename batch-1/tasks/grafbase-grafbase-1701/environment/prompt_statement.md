I'm trying to use the OpenAPI connector to generate a GraphQL schema from the Amadeus Hotel List API, but I'm running into a problem. The parser seems to completely ignore the response schemas and produces no GraphQL types or query operations at all.

After looking into it, I think the issue is that the Amadeus API uses a vendor-specific JSON content type in its response definitions rather than the plain standard JSON content type. The parser only recognizes the plain form, so it silently skips those response bodies and treats them as if they have no schema.

What I'd expect is that the parser would recognize any content type that is clearly a JSON variant — including vendor-namespaced ones — and process the response schema accordingly. The fix should allow the Amadeus hotel search spec to be fully converted into a working GraphQL schema, including all the hotel lookup query operations and the associated enum and object types for hotel data.
