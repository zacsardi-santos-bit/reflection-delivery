Implement input validation and data storage enhancements for the Radom payment integration. Ensure that checkout sessions validate required fields and handle errors clearly. Enhance the order repository to support 64-bit integer metadata storage.

*   Update the Order model:
    *   Implement the `CreateRadomCheckoutSessionWithTime` method in `services/skus/model/model.go` with the signature:
        `(o *Order) CreateRadomCheckoutSessionWithTime(ctx context.Context, client radomClient, sellerAddr string, expiresAt time.Time) (CreateCheckoutSessionResponse, error)`
    *   Validate that the order has items; return `ErrInvalidOrderNoItems` if not.
    *   Ensure the first item's metadata includes:
        *   'radom_success_uri'; return `ErrInvalidOrderNoSuccessURL` if missing.
        *   'radom_cancel_uri'; return `ErrInvalidOrderNoCancelURL` if missing.
        *   'radom_product_id'; return `ErrInvalidOrderNoProductID` if missing.
    *   Propagate errors from the Radom client using `errors.Is`.
    *   Return a `CreateCheckoutSessionResponse` with `SessionID` from the client response on success.
    *   Define the following error constants in `services/skus/model/model.go`:
        *   `ErrInvalidOrderNoItems` as `"model: invalid order: no items"`
        *   `ErrInvalidOrderNoSuccessURL` as `"model: invalid order: no success url"`
        *   `ErrInvalidOrderNoCancelURL` as `"model: invalid order: no cancel url"`
        *   `ErrInvalidOrderNoProductID` as `"model: invalid order: no product id"`

*   Enhance the Radom client package:
    *   Implement `MockClient` in `libs/clients/radom/mock.go` with:
        *   `FnCreateCheckoutSession` function field.
        *   `CreateCheckoutSession` method that calls `FnCreateCheckoutSession` if set, or returns an empty `CheckoutSessionResponse`.
    *   Ensure `CheckoutSessionResponse` in `libs/clients/radom/radom.go` includes a `SessionID` string field.

*   Update the Order repository:
    *   Implement `AppendMetadataInt64` in `services/skus/storage/repository/repository.go` with the signature:
        `(r *Order) AppendMetadataInt64(ctx context.Context, dbi sqlx.ExecerContext, id uuid.UUID, key string, val int64) error`
    *   Return `model.ErrNoRowsChangedOrder` when no order with the given ID exists, using `errors.Is`.
    *   Store the int64 value as a float64 in the order's metadata JSONB field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.