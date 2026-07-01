I'm working on improving the Radom payment processor integration in our order management system. Right now, when we try to create a Radom checkout session for an order, there's no proper validation — if the order has no items, or if the required redirect URLs or product identifier are missing from the item metadata, the failure either doesn't surface clearly or produces a confusing error message. I'd like each of these cases to produce a distinct, matchable error so callers can easily identify what went wrong.

I also need a version of the checkout session creation method that accepts an explicit expiry time instead of always deriving it from the current moment. This makes the behavior more testable and flexible.

On the storage side, I need a new method on the order repository that can append or update a 64-bit integer value by key in an order's metadata. When the target order doesn't exist, it should return a recognizable "no rows changed" error. This is needed to correctly store numeric blockchain data (like chain IDs and block numbers) received from payment webhooks.

Finally, the payment processor client package needs a simple hand-written mock implementation — with a configurable function field — so that the checkout session creation logic can be unit tested without relying on a generated mock framework.
