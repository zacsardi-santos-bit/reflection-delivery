I'm working with Active Merchant and need to add support for the Datatrans payment gateway. Datatrans is a Swiss payment service provider used in Switzerland, Greece, and the US, and it's not currently supported in Active Merchant.

I need a new gateway integration that supports the full payment lifecycle: authorizing a card, purchasing (authorize + auto-settle in one step), capturing a previously authorized transaction, refunding a settled transaction, and voiding an authorization. The gateway should also support including billing address information with authorization requests.

For security, the gateway must be able to scrub sensitive card data from transaction logs — specifically masking card numbers and CVV values with a placeholder.

The gateway should require merchant credentials (a merchant ID and password) to be provided at initialization, and raise an error if they're missing. It should declare support for common card types (Visa, Mastercard, American Express, UnionPay, Diners Club, Discover, JCB, Maestro, and Dankort) and the countries CH, GR, and US.

The integration should communicate with the Datatrans sandbox API at the appropriate endpoints for each operation: a single authorize endpoint (with auto-settle flag for purchases), plus separate settle, credit, and cancel endpoints per transaction for capture, refund, and void respectively. The default currency should be CHF.
