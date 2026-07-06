Implement support for the Adyen Checkout API in the existing Go library. Add functionality to query available payment methods and handle the environment configuration for generating correct API endpoint URLs.

*   Update the `Environment` type:
    *   Implement the `CheckoutURL(service string, version string) string` method.
        *   For test environments, return URL: `https://checkout-test.adyen.com/services/PaymentSetupAndVerification/{version}/{service}`.
        *   For production environments, return URL: `https://{prefix}-{company}-checkout-live.adyen.com/services/PaymentSetupAndVerification/{version}/{service}`.

*   Define new structs in the `adyen` package:
    *   `PaymentMethodsResponse` with fields:
        *   `PaymentMethods` of type `[]PaymentMethodDetails` (JSON: "paymentMethods").
        *   `OneClickPaymentMethods` of type `[]OneClickPaymentMethodDetails` (JSON: "oneClickPaymentMethods").
    *   `PaymentMethodDetails` with fields:
        *   `Details` of type `[]PaymentMethodDetailsInfo` (JSON: "details").
        *   `Name` of type `string` (JSON: "name").
        *   `Type` of type `string` (JSON: "type").
    *   `PaymentMethodDetailsInfo` with fields:
        *   `Items` of type `[]PaymentMethodItems` (JSON: "items").
        *   `Key` of type `string` (JSON: "key").
        *   `Type` of type `string` (JSON: "type").
    *   `PaymentMethodItems` with fields:
        *   `ID` of type `string` (JSON: "id").
        *   `Name` of type `string` (JSON: "name").
    *   `OneClickPaymentMethodDetails` with fields:
        *   `Details` of type `[]PaymentMethodTypes` (JSON: "details").
        *   `Name` of type `string` (JSON: "name").
        *   `Type` of type `string` (JSON: "type").
        *   `StoredDetails` of type `PaymentMethodStoredDetails` (JSON: "storedDetails").
    *   `PaymentMethodTypes` with fields:
        *   `Key` of type `string` (JSON: "key").
        *   `Type` of type `string` (JSON: "type").
    *   `PaymentMethodStoredDetails` with field:
        *   `Card` of type `PaymentMethodCard` (JSON: "card").
    *   `PaymentMethodCard` with fields:
        *   `ExpiryMonth`, `ExpiryYear`, `HolderName`, `Number` all of type `string`.

*   Define `PaymentMethods` struct in the `adyen` package:
    *   Must contain at least `MerchantAccount` field of type `string`.

*   Update the `Adyen` type:
    *   Implement the `Checkout() *CheckoutGateway` method to return a `CheckoutGateway`.

*   Define `CheckoutGateway` struct in `checkout_gateway.go`:
    *   Implement the `PaymentMethods(req *PaymentMethods) (*PaymentMethodsResponse, error)` method to query available payment methods from the Checkout API.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.