## Description

The NLP library currently only supports a single built-in mechanism for recognizing named entities in text. There is no way to configure it to use an external entity recognition service instead. We need to add support for an alternative entity recognition backend that delegates to an external HTTP service, enabling a richer and more flexible set of entity types.

## Expected Behavior

- Users should be able to configure the NLP manager to route entity recognition to an external service by enabling a flag in the settings.
- The external service integration should correctly detect and extract a wide variety of entity types, including: email addresses, phone numbers, URLs, numbers (integer and float), distances, quantities, temperatures, volumes, currencies, durations, and dates/times.
- Each extracted entity should include the position in the text, the matched text, an accuracy score, and a type-appropriate resolution object with the recognized value.
- If the external service is unavailable or returns an error, the system should gracefully return an empty list of entities rather than crashing.
- The component responsible for calling the external service should correctly determine the port to use based on the URL scheme (443 for HTTPS, 80 for HTTP, or whatever explicit port is provided in the URL).
- The component should be able to convert a 2-character language code to the appropriate locale format expected by the external service.

## Why This Matters

Many NLP applications require entity types that are difficult to handle with purely local, rule-based recognition. Integrating with a dedicated external entity extraction service allows the library to support a broader range of entity types with higher accuracy, while still gracefully degrading when the service is unavailable.
