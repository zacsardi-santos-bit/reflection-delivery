I'm cleaning up how Saleor's two payment systems talk to each other, the old legacy payment flow and the newer transaction-based one, because right now they step on each other and it's causing conflicts, missing audit info, and race conditions.

A few things I need fixed. When an order becomes fully paid we record an event for it, but that event's parameters don't say which payment gateway actually handled the payment, so there's no traceability. I want the fully-paid order event to include the gateway that was responsible so merchants and admins can trace how a payment got processed.

Next, when a checkout already has legacy payment records sitting on it and then a transaction gets initialized or processed against that same checkout, the old legacy payments just stay active, which lets both systems fight over the same money. Initializing or processing a transaction should automatically deactivate those existing legacy payments so we don't end up double-paying.

Also there's no guard against concurrent completion. If checkout completion has already kicked off, someone can still fire transaction init or processing at the same time and we get a race. I want those attempts rejected with a clear error telling the caller the checkout is currently being completed and the operation can't proceed while that's happening.

Last thing, the flow-selection logic for a partially-authorized checkout at completion time isn't right. If the checkout has an active legacy payment it should go down the legacy payment flow, and if it only has transaction items with no active legacy payment it should use the transaction flow. Right now when a checkout has both it may pick wrong.

All of this lives around the checkout and payment handling in Saleor (`@saleor/checkout` and `@saleor/payment`), and the point is to prevent double-payment, fix the audit trail, and kill the race conditions during transaction-based completion.
