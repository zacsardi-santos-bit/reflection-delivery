## Description

The storefront project currently relies on external library implementations for key order history and mini-cart UI components. This means that developers cannot customize the order row display, order detail sections, or cart item appearance without modifying external packages. We need locally-owned, customizable versions of these components within the project itself.

## Expected Behavior

- A locally-defined order row component should display each order's number, date, total, a thumbnail gallery of items in the order, and an order status indicator with a progress bar.
- The order row should be expandable: clicking a toggle button collapses or expands a detailed view. When expanded and not loading, full order details (shipping info, billing info, payment method, items list, and order total) should appear. When loading, the detailed area should remain empty.
- An order with shipments present, or an order with "Complete" status, should display a progress status of "Delivered" in the progress bar.
- The order details section should include a "Print Receipt" button.
- Each order item in the details view should display a product thumbnail (or a placeholder if no thumbnail is available), the product name, selected options, quantity, price, and a "Buy Again" button.
- The mini-cart product list should use a locally-defined item component that shows the product thumbnail, name, configurable options, quantity, price, stock status, and a delete button.
- The mini-cart item should show an "Out-of-stock" indicator when the product is out of stock.
- The mini-cart item should support showing a variant-specific thumbnail when configured to do so.
- The delete button in the mini-cart item should be disabled while a deletion is in progress.
- The order history page should use the local order row component rather than the one from the upstream library.

## Why This Matters

Having locally-owned implementations of these components allows the project to customize appearance and behavior without depending entirely on upstream library versions, making the storefront more maintainable and adaptable.
