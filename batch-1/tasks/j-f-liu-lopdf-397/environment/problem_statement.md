## Description

The permission flags used in PDF encryption are assigned incorrect bit positions. According to the PDF specification, certain permission bits are defined at specific positions within a 32-bit integer. The current implementation has several of these flags shifted by one bit compared to the specification, meaning encrypted documents produced by this library will have wrong permission values that won't be interpreted correctly by PDF viewers.

## Expected Behavior

- The flag that controls whether a document is printable should correspond to bit position 2 (numeric value 4)
- The flag that controls whether a document is modifiable should correspond to bit position 3 (numeric value 8)
- The flag that controls whether document content can be copied should correspond to bit position 4 (numeric value 16)
- The flag that controls whether forms can be filled should correspond to bit position 8 (numeric value 256)

## Why This Matters

When a PDF is encrypted with these incorrect permission values, any PDF viewer or library that follows the PDF specification will misinterpret which operations are allowed or disallowed. Fixing the bit positions ensures that the permission flags align with the PDF specification, so encrypted documents correctly restrict or allow user operations as intended.
