Correct the bit positions of permission flags in the PDF encryption library to align with the PDF specification. Ensure that each permission flag is set to the correct bit position so that PDF viewers interpret the permissions accurately.

*   Update the `Permissions` type in the `lopdf::encryption` module:
    *   Ensure it is a bitflags type.
    *   Correct the flag constants to match the PDF specification:
        *   `Permissions::PRINTABLE` must have a bit value of 4 (`1 << 2`).
        *   `Permissions::MODIFIABLE` must have a bit value of 8 (`1 << 3`).
        *   `Permissions::COPYABLE` must have a bit value of 16 (`1 << 4`).
        *   `Permissions::FILLABLE` must have a bit value of 256 (`1 << 8`).
*   Implement a `.bits()` method for the `Permissions` type:
    *   This method should return the underlying numeric bit representation as a `u64`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.