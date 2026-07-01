Implement the Datatrans payment gateway integration for Active Merchant to support the full payment lifecycle. Ensure the gateway handles authorization, purchase, capture, refund, and void operations, and includes billing address information with authorization requests. Implement scrubbing of sensitive card data in transaction logs.

*   Implement the `DatatransGateway` class in `lib/active_merchant/billing/gateways/datatrans.rb`.
    *   Inherit from `Gateway`.
    *   Declare `test_url = 'https://api.sandbox.datatrans.com/v1/transactions/'`.
    *   Declare `supported_cardtypes = [:master, :visa, :american_express, :unionpay, :diners_club, :discover, :jcb, :maestro, :dankort]`.
    *   Declare `supported_countries = ['CH', 'GR', 'US']`.

*   Constructor Requirements:
    *   `initialize(options = {})` must require `merchant_id` and `password`.
    *   Raise `ArgumentError` with 'Missing required parameter: merchant_id' if `merchant_id` is missing.

*   Method Implementations:
    *   `authorize(money, payment, options = {}) -> Response`
        *   POST to 'https://api.sandbox.datatrans.com/v1/transactions/authorize' in test mode.
        *   Include card number, reference number, currency (default 'CHF'), and amount in the request body.
        *   Include billing address fields if `billing_address` option is provided.
    *   `purchase(money, payment, options = {}) -> Response`
        *   Same as `authorize` but include `'autoSettle': true` in the request body.
    *   `capture(money, authorization, options = {}) -> Response`
        *   POST to 'https://api.sandbox.datatrans.com/v1/transactions/{transactionId}/settle'.
        *   Include `refno`, `currency`, and `amount` in the request body.
    *   `refund(money, authorization, options = {}) -> Response`
        *   POST to 'https://api.sandbox.datatrans.com/v1/transactions/{transactionId}/credit'.
        *   Include `refno`, `currency`, and `amount` in the request body.
    *   `void(authorization, options = {}) -> Response`
        *   POST to 'https://api.sandbox.datatrans.com/v1/transactions/{transactionId}/cancel'.
        *   Request body must be '{}'.

*   Scrubbing and Security:
    *   `supports_scrubbing?() -> true`
    *   `scrub(transcript) -> String`
        *   Replace card number and CVV with `[FILTERED]`.

*   Private Methods:
    *   `success_from(action, response) -> Boolean`
        *   Return `true` for 'settle' and 'cancel' if `response_code` is 204.
        *   Return `true` for 'authorize' and 'credit' if `transactionId` and `acquirerAuthorizationCode` are present.
    *   `message_from(succeeded, response) -> String or nil`
        *   Return `response.dig('error', 'message')` when `succeeded` is falsy.
        *   Return `nil` when `succeeded` is truthy.
    *   `url(action) -> String`
        *   Return `test_url` concatenated with `action`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.