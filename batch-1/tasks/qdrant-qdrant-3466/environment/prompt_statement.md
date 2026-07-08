I'm adding a new filter condition type to Qdrant's filtering system. Right now I can combine conditions with must (all match), should (at least one matches), or must_not (none match), but there's no way to say "at least N out of M of these must match." That comes up constantly in real usage, like when I want points matching at least 2 out of 3 characteristics (say city, color, and count) without manually enumerating every valid pair. It's way more expressive than all-or-nothing.

So I want a new clause, something like a min_should that takes a list of conditions plus a minimum count threshold, and a point passes it if and only if it satisfies at least that many conditions from the list. It needs to play nice with the existing filter types, so I can drop it alongside must conditions in the same filter object, or negate it by putting it in a must_not context to exclude points that hit the threshold, and it should work in nested filter contexts too, not just top level.

One validation thing: if someone sends the condition list but forgets the minimum count, the API should reject that with a validation error (HTTP 400) rather than quietly accepting it.

On the internal side, query planning in the cardinality estimation code needs to handle this new filter type correctly when it estimates how many points a query will touch. Oh and one property I care about: when the minimum count equals the total number of listed conditions, the estimate should come out identical to treating them all as required must conditions, since that's logically the same thing.

The point of all this is letting people express threshold-based filtering without resorting to big manually enumerated combinations of the existing types, which makes the whole filter language more usable for real scenarios.
