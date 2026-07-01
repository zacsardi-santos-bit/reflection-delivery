## Add Datatrans Payment Gateway Integration

## Description

Active Merchant currently does not support the Datatrans payment gateway. Datatrans is a widely-used Swiss payment service provider that supports merchants in Switzerland, Greece, and the United States. Without this integration, developers cannot use Active Merchant to process payments through Datatrans.

## Expected Behavior

A new gateway should be added to Active Merchant that supports:

- **Authorization**: Sending authorization requests to the Datatrans API with card details, reference number, currency, and amount
- **Purchase**: Combining authorization and settlement in a single step (auto-settle)
- **Capture**: Settling a previously authorized transaction using the transaction identifier
- **Refund**: Crediting a previously settled transaction
- **Void**: Cancelling an authorized (but not yet settled) transaction
- **Billing address support**: Optionally including billing address details (name, street, city, country, phone, postal code, email) with authorization requests
- **Transcript scrubbing**: Masking sensitive card data (card number and CVV) in transaction logs

## Supported Configuration

- The gateway should support cards of types: Visa, Mastercard, American Express, UnionPay, Diners Club, Discover, JCB, Maestro, and Dankort
- Supported countries: Switzerland (CH), Greece (GR), and United States (US)
- Default currency: CHF
- Both sandbox (test) and live environments should be supported

## Why This Matters

Merchants using Datatrans as their payment processor should be able to integrate with Active Merchant like any other supported gateway, with proper error handling and data security.
