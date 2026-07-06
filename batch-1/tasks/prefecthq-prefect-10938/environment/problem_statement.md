## Description

When using block documents in Prefect configuration templates, there is currently no way to navigate into nested data within a block's stored content. If a block document contains a structured value — such as a dictionary with multiple keys or a list of items — templates can only retrieve the entire block value, not a specific nested field or list element.

This limitation forces users to create a separate block for every individual value they need to reference, even when all those values logically belong together in one structured document. It also prevents using block references in deployment configuration files when the relevant setting is nested inside a block's data.

## Expected Behavior

- Block references in templates should support dot notation to navigate into nested dictionary keys at any depth.
- Block references should support bracket-notation to access list elements by numeric index.
- Dot and bracket notation should be combinable freely for complex nested structures.
- For blocks that store their primary content under a standard field, the existing shorthand template syntax (without specifying that field name explicitly) should continue to resolve correctly, providing backwards compatibility.
- Accessing a named attribute of a block directly by name in the keypath should return the attribute's value from the block's stored data.
- When the specified keypath does not exist in the block's data (missing key, out-of-range index, or absent attribute), a clear error indicating the path could not be resolved should be raised rather than silently returning an incorrect result.

## Why This Matters

Users managing complex deployment configurations should be able to store related settings together in a single block document and reference individual values from that block by specifying a path. This reduces block proliferation and makes configuration easier to maintain.
