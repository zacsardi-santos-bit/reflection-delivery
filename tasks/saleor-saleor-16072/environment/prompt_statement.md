I'm working on improving how Saleor handles the interaction between its legacy payment flow and the newer transaction-based payment flow. There are several related issues I need to address.

First, when an order becomes fully paid, the event recorded for that should include which payment gateway was used. Right now that information isn't stored in the event parameters at all, which makes it hard to trace how a payment was processed.

Second, when a checkout has existing legacy payment records and a transaction is then initialized or processed on it, those legacy payments should be automatically deactivated. Leaving them active can cause conflicts between the two payment systems.

Third, there's no protection against race conditions when checkout completion is already underway. If checkout completion has already started, any attempt to initialize or process a transaction on that checkout should be rejected with a clear error — something that tells the caller the checkout is currently being completed and the operation cannot proceed.

Finally, the logic that picks which payment flow to use when completing a partially-authorized checkout needs to be correct: if the checkout has an active legacy payment, it should go through the legacy payment flow; if it only has transaction items (and no active legacy payment), it should use the transaction flow.
