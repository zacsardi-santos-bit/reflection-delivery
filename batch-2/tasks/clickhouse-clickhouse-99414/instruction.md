I ran into a server crash triggered by the UTF-8 string reversal function in ClickHouse.

*   The reverseUTF8 function implementation in src/Functions/reverseUTF8.cpp must add bounds checking to handle truncated multi-byte UTF-8 sequences without crashing or reading out of bounds.

*   The implementation must include a bounds check that compares the computed character length against the number of remaining bytes in the string, detecting when a leading byte indicates a multi-byte character but insufficient continuation bytes are available.

*   When an incomplete multi-byte UTF-8 sequence is detected (the needed character length exceeds remaining bytes), the function must treat the leading byte as a single byte instead of attempting to copy multiple bytes.

*   The function must continue to correctly reverse valid UTF-8 strings: reversing 'ClickHouse' yields 'esuoHkcilC', reversing the Russian word for hello yields the character-reversed form, and reversing the Japanese greeting yields the character-reversed form.


*   Interface details: Type: Implementation File
Name: reverseUTF8 implementation
Location: src/Functions/reverseUTF8.cpp
Description: The C++ implementation of the reverseUTF8 SQL function. This file must be modified to add bounds checking for truncated multi-byte UTF-8 sequences. The implementation must include logic that detects when the computed character length exceeds the number of bytes remaining in the string and, in that case, treats the leading byte as a single byte instead of attempting to read beyond the available data.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.