I'm working with an Adyen payment library written in Go and I need to add support for the Checkout API. Right now the library only handles the classic payment authorization flow, but I need to be able to retrieve available payment methods for a merchant through the newer Checkout API.

I need the library to support querying available payment methods and properly deserializing the response, which can include regular payment methods (like credit cards, iDEAL with a selectable bank list, SEPA, Klarna, etc.) as well as stored one-click payment methods with saved card details (expiry month, expiry year, cardholder name, and card number). The response should correctly map all of these nested structures from the API's JSON format.

I also need the environment configuration to know about the Checkout API endpoint URL. For the test environment, it should point to the Adyen checkout test host. For production environments, the URL should incorporate the live endpoint prefix and company name that are already used for other production URLs.

Could you add the necessary types, gateway, and environment URL support to make the Checkout API's payment methods endpoint accessible through this library?
