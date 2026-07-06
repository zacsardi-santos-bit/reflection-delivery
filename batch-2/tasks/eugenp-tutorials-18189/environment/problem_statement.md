## Description

The currency converter module is missing a data model class for representing responses from an external exchange rate API. Without this class, the rest of the application cannot parse or use exchange rate data returned by the API, causing compilation and runtime failures across the service layer.

## Expected Behavior

- A data holder class for exchange rate responses should exist in the appropriate data transfer object package within the currency converter module
- When created without arguments, both the base currency field and the rates field should default to null
- The class should support setting and retrieving the base currency via standard accessor methods
- The class should expose the exchange rates field via a standard accessor method, returning null when no rates have been set

## Why This Matters

The currency conversion service depends on this class to deserialize API responses and extract the target currency exchange rate. Without it, the service layer cannot function, and the entire conversion feature is broken.
