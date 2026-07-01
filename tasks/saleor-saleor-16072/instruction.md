Implement enhancements to the Saleor payment system to address issues with order events, legacy payment deactivation, race conditions, and payment flow selection. Ensure the system records payment gateway information, deactivates legacy payments appropriately, prevents concurrent operations during checkout completion, and selects the correct payment flow for partially-authorized checkouts.

*   Update the `handle_fully_paid_order` function in `saleor/order/actions.py`:
    *   Accept an optional `gateway` keyword argument.
    *   Include `{"payment_gateway": gateway}` in the `ORDER_FULLY_PAID` event parameters when `gateway` is provided.
    *   Ensure the function receives the payment gateway identifier during the order confirm mutation flow.

*   Modify the `complete_checkout` function in `saleor/checkout/complete_checkout.py`:
    *   Call `complete_checkout_with_transaction` if `checkout.authorize_status == CheckoutAuthorizeStatus.PARTIAL` and no active payment exists.
    *   Call `complete_checkout_with_payment` if `checkout.authorize_status == CheckoutAuthorizeStatus.PARTIAL` and at least one active payment exists.

*   Enhance the `TransactionInitialize` mutation in `saleor/graphql/payment/mutations/transaction/transaction_initialize.py`:
    *   Return an error with code `CHECKOUT_COMPLETION_IN_PROGRESS` and field 'id' if `checkout.completing_started_at` is set.
    *   Deactivate all existing Payment records (set `is_active=False`) upon successful transaction initialization.

*   Enhance the `TransactionProcess` mutation in `saleor/graphql/payment/mutations/transaction/transaction_process.py`:
    *   Return an error with code `CHECKOUT_COMPLETION_IN_PROGRESS` and field 'id' if `checkout.completing_started_at` is set, without processing the transaction.
    *   Deactivate all existing Payment records (set `is_active=False`) upon successful transaction processing.

*   Add `CHECKOUT_COMPLETION_IN_PROGRESS` to `TransactionInitializeErrorCode` and `TransactionProcessErrorCode` enums in `saleor/payment/error_codes.py`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.