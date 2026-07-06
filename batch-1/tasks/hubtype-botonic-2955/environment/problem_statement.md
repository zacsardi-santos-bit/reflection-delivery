## Description

We need to add support for several WhatsApp-specific message types that allow bots to send rich commerce content to users. Currently, the framework has no way to compose single product messages, product list messages (with multiple sections), product carousels, or media carousels for WhatsApp. Bot developers are unable to leverage these WhatsApp features through the standard component model.

## Expected Behavior

- A single-product message component that accepts product identity and display information and renders the appropriate message element
- A product list component that accepts header, footer, body text, and a list of sections — where each section has a title and a list of products — and serializes the data with properly formatted keys for the WhatsApp API
- A product carousel component that accepts a template name, template language, body parameters, and a list of product cards — where each card specifies a product; card positions should be assigned automatically when not explicitly provided
- A media carousel component that accepts a template name, template language, body parameters, and a list of image-based cards — where each card has media info, buttons, and body parameters; card and button positions should be assigned automatically when not explicitly provided

## Implementation Notes

A utility function for deep conversion of object keys from camelCase to snake_case format will be needed to correctly serialize the structured card and section data that WhatsApp's API expects. This function should handle nested objects, arrays within objects, and numeric suffixes in key names. It should return undefined when given undefined as input.

## Why This Matters

Without these components, bot developers building WhatsApp experiences have no structured way to send product or media carousel messages. They would need to manually construct low-level message structures instead of using the idiomatic component model used throughout the rest of the framework.
