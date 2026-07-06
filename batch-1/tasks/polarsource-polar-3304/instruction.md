Implement a new checkout module to manage subscription product checkout sessions, separating it from the existing subscriptions module. Create API endpoints for creating and retrieving checkout sessions, ensuring proper input validation and handling for different user authentication scenarios.

*   Create a new checkout module:
    *   Export a service instance named `checkout` from `polar/checkout/service.py`.
    *   Define an `AlreadySubscribed` exception class in the same file.
    *   Import `stripe_service` from `polar.integrations.stripe.service` as `stripe_service`.

*   Define schemas:
    *   Create a `CheckoutCreate` schema in `polar/checkout/schemas.py` with fields:
        *   `product_price_id`: UUID, required.
        *   `success_url`: pydantic Url, required.
        *   `customer_email`: Optional[str], defaults to None.

*   Implement the `checkout.create` method:
    *   Raise `PolarRequestValidationError` if `product_price_id` does not exist, or if the price/product is archived.
    *   Raise `AlreadySubscribed` if the user already has an active subscription to the product.
    *   Call `stripe_service.create_checkout_session` with appropriate parameters based on user authentication:
        *   Anonymous or OAuth2 token users (without email): `is_subscription=True`, `is_tax_applicable=False`, metadata with `product_id` and `product_price_id`.
        *   Cookie-authenticated users: Include `customer` set to `stripe_customer_id` and `user_id` in metadata.
        *   OAuth2 token users with `customer_email`: Pass `customer_email` and metadata with `product_id` and `product_price_id`.
    *   Enable `is_tax_applicable` if the product has tax-applicable benefits.
    *   Include `subscription_id` in metadata if upgrading from a free subscription.

*   Implement the `checkout.get_by_id` method:
    *   Call `stripe_service.get_checkout_session` with the session ID.
    *   Raise `ResourceNotFound` if session metadata is invalid or incomplete.
    *   Return a checkout object with `id`, `url`, `customer_email`, `customer_name`, `product`, and `product_price`.

*   Update `StripeService`:
    *   Replace `create_subscription_checkout_session` with `create_checkout_session` in `polar/integrations/stripe/service.py`.
    *   Method signature: `create_checkout_session(price: str, success_url: str, *, is_subscription: bool, is_tax_applicable: bool, customer: str | None = None, customer_email: str | None = None, metadata: dict | None = None, subscription_metadata: dict | None = None)`.

*   Create HTTP endpoints:
    *   POST `/api/v1/checkouts/`:
        *   Validate inputs and return HTTP 422 for errors.
        *   Return HTTP 201 on success with JSON response containing `id`, `url`, `product`, and `product_price`.
    *   GET `/api/v1/checkouts/{id}`:
        *   Return HTTP 200 on success with JSON response containing `id`, `url`, `customer_name`, `customer_email`, `product`, and `product_price`.

*   Remove old subscribe-session endpoints and services from the subscriptions module.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.