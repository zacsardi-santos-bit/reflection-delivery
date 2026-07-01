Fix the checkout completion logic to ensure accurate discount calculations when both a voucher and a gift card are used. Implement changes to correctly calculate the voucher discount amount and the undiscounted order total, ensuring they are not inflated by the gift card's contribution.

*   Ensure the order's undiscounted total includes the original shipping price before any voucher discounts.
    *   For free-shipping vouchers, the undiscounted total should be the subtotal plus the original shipping price.
*   Set the order's shipping price to zero when a free-shipping voucher is used, while maintaining the original shipping price in the undiscounted total.
*   Calculate the order discount record of type VOUCHER accurately:
    *   For percentage vouchers, subtract the gift card's initial balance from the difference between the undiscounted total and the order total.
    *   For free-shipping vouchers, set the amount_value to the original shipping price, excluding any gift card reductions.
    *   For product-specific and apply-once-per-order vouchers, follow the same calculation as percentage vouchers.
*   Manage gift card usage:
    *   Reduce the gift card's current balance to zero.
    *   Set the gift card's last_used_on field.
    *   Create a GiftCardEvent of type USED_IN_ORDER.
*   Increment the voucher's usage count by one after order creation.
*   Delete the checkout record once the order is successfully created.
*   Apply the same discount accounting rules for the `orderCreateFromCheckout` mutation:
    *   Exclude the gift card's contribution from the voucher's recorded discount amount.
    *   Use the original pre-voucher shipping price for the undiscounted total.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.