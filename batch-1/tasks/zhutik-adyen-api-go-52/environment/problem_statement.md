## Description

The library currently supports Adyen's classic Payment Authorization API but has no support for the newer Checkout API. Developers building modern checkout flows need to be able to retrieve the available payment methods for a merchant — such as credit cards, iDEAL, SEPA, Klarna, stored cards, etc. — but the library provides no way to do this today.

Additionally, the environment configuration has no concept of a Checkout API endpoint, meaning there is no way to generate the correct URLs for test or production Checkout API calls.

## Expected Behavior

- The library should provide a way to retrieve available payment methods from the Checkout API, returning a structured response with full details of each method including its type, display name, and any input fields required.
- Stored/one-click payment methods should also be included in the response with their associated card details (expiry month, expiry year, cardholder name, last four digits).
- The environment configuration should be able to generate correct Checkout API endpoint URLs for both test and production environments. Production environments use a live endpoint prefix and company name to build the URL.
- The response structures should be able to correctly parse JSON from the Checkout API, including nested details, selectable items (such as bank selection lists for iDEAL), and optional fields.

## Why This Matters

Merchants using Adyen increasingly rely on the Checkout API to present relevant payment options to shoppers. Without this support, developers using this library must implement their own HTTP calls and response parsing outside the library, leading to inconsistency and duplicated effort.
