## Description

When filtering cables by site or location, any cable that has one or more endpoints connected via a circuit termination is silently omitted from the results, even when that circuit termination genuinely belongs to the queried site or location.

This is a data-correctness bug. The system maintains cached site and location information on each cable endpoint record so that site/location filters can run efficiently. However, when the endpoint is a circuit termination, the caching logic only captures the site (and even then, via the wrong field), while completely ignoring the location. As a result, cables whose endpoints are circuit terminations are effectively invisible to site and location filters.

## Expected Behavior

- A cable connected on one or both ends via a circuit termination should appear in results when filtering cables by the site or location associated with that circuit termination.
- Both the site and the location of a circuit termination must be captured and stored on the corresponding cable endpoint record.
- A circuit termination that is associated with a specific location should cause any attached cable to be retrievable by that location filter.
- A circuit termination that is associated with a specific site (directly or through a location) should cause any attached cable to be retrievable by that site filter.

## Why This Matters

Network managers rely on site and location filters to get a complete picture of the cabling in a given area. When cables connected to circuits are silently excluded, those managers are working with incomplete data, which can cause confusion and errors during infrastructure planning and troubleshooting.
