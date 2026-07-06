Implement a string encoding function for encrypted full-text search indexing. This function should convert a plaintext string into sets of encoded text fragments, including substrings, prefixes, and suffixes, based on specified parameters. Ensure the encoding process handles multi-byte Unicode characters correctly and rejects invalid UTF-8 strings.

*   Implement `mc_text_search_str_encode`:
    *   Accepts a `mc_FLE2TextSearchInsertSpec_t` and `mongocrypt_status_t`.
    *   Returns a `mc_str_encode_sets_t` with encoded suffix, prefix, and/or substring sets.
    *   Returns NULL and sets an error if the input is not valid UTF-8, with the message containing "not valid UTF-8".
    *   Correctly handles empty strings, treating unfolded codepoint length as 1.
*   Implement `mc_text_search_str_encode_helper`:
    *   Accepts a `mc_FLE2TextSearchInsertSpec_t`, an unfolded codepoint length (uint32_t), and `mongocrypt_status_t`.
    *   Behaves like `mc_text_search_str_encode` but uses the provided unfolded codepoint length.
    *   Returns NULL and sets an error if unfolded codepoint length exceeds the `mlen` from the substring spec, with the message containing "longer than the maximum length".
*   Implement `mc_str_encode_sets_destroy`:
    *   Frees all memory associated with a `mc_str_encode_sets_t`.
    *   Tolerates being passed NULL.
*   Ensure `mc_str_encode_sets_t`:
    *   `base_string` points to a `mc_utf8_string_with_bad_char_t` with `buf.len` equal to input byte length plus one.
    *   `exact` field is a non-owning view into `base_string->buf.data`.
*   Handle index type specifications:
    *   Populate only the requested set (suffix, prefix, or substring) unless `lb > max_padded_len`.
    *   Calculate `max_padded_len` as `16 * ceil(unfolded_codepoint_len / 16)`.
*   Implement affix and substring iteration:
    *   Use `mc_affix_set_iter_next` and `mc_substring_set_iter_next` for iterating over entries.
    *   Ensure pointers returned are direct views into `base_string->buf.data`.
    *   Order entries from smallest to largest length, with padding entries last.
*   Deduplicate substrings:
    *   Each unique byte sequence appears once with count 1.
    *   Total count of entries in a substring set must match calculated expectations.
*   Handle multi-byte UTF-8 strings:
    *   Compute codepoint counts and offsets on a per-codepoint basis.
*   Support simultaneous requests for all index types:
    *   Populate all sets independently and correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.