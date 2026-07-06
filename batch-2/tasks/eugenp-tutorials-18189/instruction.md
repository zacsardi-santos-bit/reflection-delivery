Implement the `ExchangeRateResponse` class in the specified package to represent responses from an exchange rate API. Ensure it includes a no-argument constructor that initializes fields to null and provides standard accessor methods for its properties.

*   Create the `ExchangeRateResponse` class in the `com.baeldung.currencyconverter.dto` package.
    *   Implement a no-argument constructor that initializes all fields to null.
*   Implement the following methods:
    *   `String getBase()`: Return the base currency string, defaulting to null if not set.
    *   `void setBase(String base)`: Set the base currency string.
    *   `Map<String, Double> getRates()`: Return the exchange rates map, defaulting to null if not set.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.