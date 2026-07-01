Implement enhancements to the payment flow in the e-commerce backend to improve event logging, flow selection, and transaction processing. Ensure the system correctly records payment gateway information, prioritizes legacy payment flows, and handles checkout locks and payment deactivation.

*   Update the `handle_fully_paid_order` function in `saleor/order/actions.py`:
    *   Add an optional `gateway` parameter (type: Optional[str], default: None).
    *   Include the gateway identifier in the `ORDER_FULLY_PAID` event's parameters as `{"payment_gateway": gateway}` when provided.

*   Modify the `order_fully_paid_event` function in `saleor/order/events.py`:
    *   Add an optional `gateway` parameter (type: Optional[str], default: None).
    *   Create the `OrderEvent` with parameters `{"payment_gateway": gateway}` when the gateway is provided.

*   Adjust the order confirmation logic:
    *   When a payment fully authorizes an order, pass the payment's gateway identifier to `handle_fully_paid_order` as the 6th positional argument.

*   Enhance the `complete_checkout` function in `saleor/checkout/complete_checkout.py`:
    *   Route to the transaction flow when the checkout is fully authorized or when transaction items exist without an active payment.
    *   Route to the legacy payment flow when an active payment exists, unless the checkout is fully authorized.

*   Update error handling in `saleor/payment/error_codes.py`:
    *   Add `CHECKOUT_COMPLETION_IN_PROGRESS` to `TransactionInitializeErrorCode` and `TransactionProcessErrorCode` with the value 'checkout_completion_in_progress'.

*   Implement checkout state validation in GraphQL mutations:
    *   For `transactionInitialize` in `saleor/graphql/payment/mutations/transaction/transaction_initialize.py`:
        *   Validate the checkout state before processing.
        *   Return an error with code `CHECKOUT_COMPLETION_IN_PROGRESS` if `completing_started_at` is set.
        *   Deactivate active payments using `cancel_active_payments(checkout)` within a `traced_atomic_transaction`.

    *   For `transactionProcess` in `saleor/graphql/payment/mutations/transaction/transaction_process.py`:
        *   Validate the checkout state before processing.
        *   Return an error with code `CHECKOUT_COMPLETION_IN_PROGRESS` if `is_checkout_locked()` returns True.
        *   Deactivate active payments using `cancel_active_payments(checkout)` within a `traced_atomic_transaction`.

*   Implement the `cancel_active_payments` function in `saleor/checkout/utils.py`:
    *   Set `is_active=False` on all active payments associated with the given checkout.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.