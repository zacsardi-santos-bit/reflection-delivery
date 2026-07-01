Create locally-owned versions of the order history and mini-cart UI components to replace the existing reliance on external libraries. Implement the OrderRow and OrderDetails components for the order history page, and the ProductList and Item components for the mini-cart.

*   Implement the `OrderRow` component in `ui.frontend/src/main/components/OrderHistoryPage/orderRow.js`.
    *   Accept an `order` prop and render order number, date (formatted as M/D/YYYY), total, a collapsed image gallery, status badge, progress bar, and a toggle button.
    *   Render a collapsed content area when `isOpen` is false, and an expanded area with `OrderDetails` when `isOpen` is true.
    *   Render an empty content area when `loading` is true.
    *   Map orders with 'Complete' status or shipments present to a progress bar status of 'Delivered'.

*   Implement the `OrderDetails` component in `ui.frontend/src/main/components/OrderHistoryPage/OrderDetails/orderDetails.js`.
    *   Accept `imagesData` and `orderData` props.
    *   Render shipping information, shipping method, billing information, payment method, an items list, order total, and a "Print Receipt" button.

*   Implement the `Items` component in `ui.frontend/src/main/components/OrderHistoryPage/OrderDetails/items.js`.
    *   Accept a `data` prop containing `items` and `imagesData`.
    *   Render a heading "Items" and each order item as an `Item` component.

*   Implement the `Item` component in `ui.frontend/src/main/components/OrderHistoryPage/OrderDetails/item.js`.
    *   Accept `product_name`, `product_sale_price`, `product_url_key`, `quantity_ordered`, `selected_options`, and `thumbnail` props.
    *   Render the product thumbnail (or a placeholder if not available), name, selected options, quantity, price, and a "Buy Again" button.

*   Implement the `ProductList` component in `ui.frontend/src/main/components/MiniCart/ProductList/productList.js`.
    *   Accept `closeMiniCart`, `handleRemoveItem`, and `items` props.
    *   Render each item using the `Item` component, passing necessary props.

*   Implement the `Item` component in `ui.frontend/src/main/components/MiniCart/ProductList/item.js`.
    *   Accept `product`, `id`, `quantity`, `configurable_options`, `handleRemoveItem`, `prices`, and optionally `configurableThumbnailSource`.
    *   Render a thumbnail, product name, configurable options, quantity, price, stock status, and a delete button.
    *   Display "Out-of-stock" when `stock_status` is 'OUT_OF_STOCK'.
    *   Use the variant's thumbnail URL when `configurableThumbnailSource` is provided and a matching variant exists.
    *   Disable the delete button when `isDeleting` is true.

*   Update the `orderHistoryPage` component to import and use the local `OrderRow` component from `../orderRow`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.