# Improve Bloom Filter Support: BTF Map Type and API Fix

## Description

The bloom filter map type in the aya library has a couple of related problems that need to be addressed together.

First, the user-space API for checking whether a value exists in a bloom filter unnecessarily requires the caller to pass the value as a mutable reference. Since checking for membership doesn't modify the value, requiring mutability is an unnecessary burden on users and makes it harder to use the API in contexts where the value isn't mutable.

Second, bloom filters are keyless maps — they have values but no key type. When the library encounters a bloom filter that was created with type information (identifying the value type), it fails to recognize it as a typed map because it checks only for the presence of a key type. This causes two problems:

- When reading map information back from a running system, bloom filters with value type information are misidentified as legacy (untyped) maps instead of typed maps.
- When creating a typed bloom filter, the library incorrectly provides key type information to the kernel, even though the kernel expects a void key for bloom filters.

Additionally, there is no BTF-typed bloom filter map type available for use in eBPF programs on the kernel side, making it impossible to declare bloom filter maps using the typed map pattern that other map types support.

## Expected Behavior

- Checking for membership in a bloom filter should work without requiring the queried value to be declared as mutable.
- A bloom filter map with value type information (but no key type) should be correctly recognized as a typed map when parsed from kernel-reported map info.
- When creating a typed bloom filter, the key type should be set to void as the kernel requires.
- Per-map extra metadata (used to configure the number of hash functions) should be correctly preserved in the map definition and passed through when parsing and creating bloom filter maps.
- eBPF programs should be able to declare bloom filter maps using the same const-generic typed map pattern as other BTF map types.

## Why This Matters

These issues prevent bloom filters from working correctly in the typed map workflow, and make the user-space API unnecessarily cumbersome. Fixing them allows bloom filters to be fully first-class citizens in the typed map system.
