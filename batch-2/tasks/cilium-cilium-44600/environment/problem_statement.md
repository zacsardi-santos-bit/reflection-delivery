## Description

The DNS proxy pipeline currently passes raw DNS protocol messages through its internal components, requiring every handler to re-parse the same message to extract relevant information — query names, IP addresses, TTL values, CNAME chains, response codes, and record types. This creates redundant work and an implicit interface that is hard to reason about. More importantly, it creates an ambiguity between DNS requests and responses: a handler receiving a raw message must determine which fields are valid based on message direction, and there is no enforcement preventing response-specific data from being read out of a request message that has been spoofed with an answer section.

## Expected Behavior

- A single, well-defined structured type should be used to pass parsed DNS information through the pipeline instead of raw messages.
- There should be separate extraction functions for DNS requests and DNS responses, each populating only the fields that are valid for that direction.
- When a DNS request is parsed, response-specific fields (resolved IPs, CNAME chains, TTL, answer record types) should remain unset — even if the raw request message contains a spoofed answer section.
- Downstream handlers should work with this pre-parsed structure rather than parsing the raw message themselves.
- Handlers that receive a nil endpoint should return an error rather than silently producing incorrect output.

## Why This Matters

Passing raw DNS messages between components creates a leaky abstraction that forces consumers to understand DNS wire format internals. Separating request parsing from response parsing enforces correctness at the type boundary and prevents request spoofing by ensuring request handlers cannot accidentally access fabricated response data.
