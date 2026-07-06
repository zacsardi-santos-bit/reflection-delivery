Implement support for creating orders from mobile in-app purchase receipts for the Leo premium AI assistant product. Ensure mobile subscription identifiers from Android and iOS are correctly mapped to internal product SKUs, and handle the full receipt-to-order process with proper error handling.

*   Implement `collectValidationErrors` in `services/skus/`:
    *   Accept an error and return a map of field names to validation error message strings plus a boolean.
    *   Return `(nil, false)` for nil errors or non-struct validation errors.
    *   Return `(map[string]string, true)` with field names as keys and validation error messages as values when validation errors are present.

*   Implement `parseSubmitReceiptRequest` in `services/skus/`:
    *   Accept raw bytes containing a base64-encoded JSON payload.
    *   Decode into `model.ReceiptRequest`, mapping JSON field 'type' value 'android' to `model.VendorGoogle` and 'raw_receipt' to the Blob field.

*   Implement `handleReceiptErr` in `services/skus/`:
    *   Accept an error and return a `*handlers.AppError`.
    *   For nil error, return an AppError with Message 'Unexpected error', Code 500, and an empty Data map.
    *   For sentinel errors like `errPurchaseFailed`, return a 400 AppError with specific error codes and validationErrors data.

*   Define `model.ReceiptRequest` struct in `services/skus/model/`:
    *   Include fields: Type (validated with 'oneof' tag), Blob (required), Package, and SubscriptionID.
    *   Ensure it produces specific error messages for invalid fields.

*   Implement `newMobileOrderMdata` in `services/skus/`:
    *   Accept a `model.ReceiptRequest` and an external ID string.
    *   Return a `datastore.Metadata` map with specific values based on the vendor.

*   Implement `newOrderNewForReq` in `services/skus/`:
    *   Accept a `*model.CreateOrderRequestNew`, a list of `model.OrderItem`, a merchant ID, and a status.
    *   Return `model.ErrInvalidOrderRequest` if items are empty.
    *   Compute `TotalPrice`, handle zero-total orders, and set `Location` and `ValidFor` appropriately.

*   Implement `createOrderWithReceipt` in `services/skus/`:
    *   Accept a context, a `paidOrderCreator`, order item request templates, a `*premiumPaymentProcConfig`, a `model.ReceiptRequest`, and an external ID string.
    *   Return `model.ErrInvalidMobileProduct` if the receipt's SubscriptionID cannot be resolved.
    *   Propagate errors from `createOrder` and `UpdateOrderStatusPaidWithMetadata`.

*   Implement `skuNameByMobileName` in `services/skus/`:
    *   Map mobile subscription identifiers to SKU names.
    *   Return `model.ErrInvalidMobileProduct` for unrecognized names.

*   Implement `newOrderItemReqForSubID` in `services/skus/`:
    *   Accept a map of SKU names to `model.OrderItemRequestNew` and a subscription ID string.
    *   Return `model.ErrInvalidMobileProduct` if the subscription ID is unrecognized or the SKU name is not present.

*   Implement `newCreateOrderReqNewLeo` in `services/skus/`:
    *   Accept a `*premiumPaymentProcConfig` and a `model.OrderItemRequestNew`.
    *   Return a `model.CreateOrderRequestNew` with specific fields set.

*   Implement `newOrderItemReqNewLeoSet` in `services/skus/`:
    *   Accept an environment string and return a map of SKU names to `model.OrderItemRequestNew`.
    *   Set environment-specific values for production, staging, and other environments.

*   Implement `newPaymentProcessorConfig` in `services/skus/`:
    *   Accept an environment string and return a `*premiumPaymentProcConfig`.
    *   Set environment-specific payment processor redirect URIs.

*   Define `premiumPaymentProcConfig` struct in `services/skus/`:
    *   Include fields `successURI` and `cancelURI`.

*   Define `paidOrderCreator` interface in `services/skus/`:
    *   Include methods `createOrder` and `UpdateOrderStatusPaidWithMetadata`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.