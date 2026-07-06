## Description

Webpack's HTML processing pipeline currently has no way to decode HTML character encodings — the patterns used in HTML to represent special characters as named references or numeric codes. This is a problem when webpack needs to resolve the actual value of an attribute or URL: a link address that was authored using the encoded ampersand form needs to be decoded back to a literal ampersand before it can be used as a resource path.

Additionally, the HTML tokenizer should properly handle all forms of HTML character encoding — named references, decimal numeric references, and hexadecimal numeric references — so it can step over them without confusing them with other HTML syntax.

## Expected Behavior

- A decoding utility should be available as part of the HTML tokenizer module to convert HTML-encoded strings back to their actual character values.
- The decoder must handle the common named encodings (ampersand, less-than, greater-than, double-quote, apostrophe, non-breaking space) as well as both decimal and hexadecimal numeric forms.
- Unknown or malformed encodings should be preserved as-is to prevent silent data loss.
- The HTML tokenizer must correctly recognize and skip over these encoding sequences while scanning HTML content.
- Numeric character encodings in srcset attribute values should NOT be decoded globally — doing so would break srcset parsing for whitespace-encoded values.

## Why This Matters

Without a decoding utility, webpack cannot correctly derive the actual URL or text content from HTML attributes that use character encoding. This is a common pattern in real-world HTML, especially for query string parameters in URLs where ampersands are encoded. The decoding needs to be selective — not applied globally during tokenization — to preserve correctness for attribute types like srcset where encoding semantics must be respected.
