I'm working on fixing several related payment flow issues in our e-commerce backend.

First, when an order gets fully paid after a payment is captured, the order event that records this has no payment gateway information in it. We need that event to include the gateway identifier so it's easy to see which payment provider was used when reviewing order history.

Second, there's a routing bug in checkout completion. When a checkout has both modern transaction-based payment items and a legacy active payment, the system incorrectly picks the transaction-based flow to complete the checkout instead of the legacy payment flow. The selection logic should prefer the legacy payment flow whenever an active payment is present on the checkout.

Third, if someone tries to initialize or process a new payment transaction on a checkout that is already in the process of being completed (locked), the operation goes through without any error. Instead, it should immediately reject the request with a specific error indicating that checkout completion is already in progress, and not proceed to process anything.

Finally, when a transaction is initialized or processed on a checkout that has existing active legacy payments, those legacy payments stay active. This creates a risk of the checkout being completed through two different payment mechanisms. When processing a transaction on a checkout, all active legacy payments should be automatically deactivated in the same atomic operation.
