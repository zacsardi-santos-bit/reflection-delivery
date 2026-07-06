# Incorrect Voucher Discount Amount When Gift Card Is Also Used at Checkout

## Description

When a customer completes a checkout using both a voucher and a gift card, the order discount record for the voucher is being calculated incorrectly. The system is attributing the gift card's contribution to the voucher discount, resulting in an inflated (or otherwise wrong) voucher discount amount saved on the order.

This is particularly visible with free-shipping vouchers: when a customer uses a free-shipping voucher together with a gift card, the order's "undiscounted total" is computed as if the shipping was already zero — because the discounted shipping price is used instead of the original shipping price. This causes the voucher discount amount to be wrong, and may cause downstream errors in order totals and financial reporting.

## Expected Behavior

- When an order is created from a checkout that uses both a voucher and a gift card, the voucher's recorded discount amount should reflect only the voucher's contribution — the gift card's portion of the total discount must not be included in the voucher discount record.
- The order's undiscounted total should always use the original, pre-voucher shipping price, even when a free-shipping voucher was applied.
- For a free-shipping voucher, the order's shipping price should be zero, but the undiscounted total should still include the original shipping price.
- Gift cards should be fully consumed, and the appropriate gift card usage event should be recorded.

## Why This Matters

Voucher usage tracking and order financial summaries depend on accurate discount breakdowns. When gift cards and vouchers are combined, the current behavior conflates two separate discount sources into one, leading to incorrect voucher discount records that affect analytics, reporting, and potentially refund calculations.
