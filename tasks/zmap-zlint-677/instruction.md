Implement the necessary changes to fix the Tor onion address linting logic in the certificate linting tool. Ensure that the tool correctly distinguishes between version 2 and version 3 Tor addresses and handles mixed certificates appropriately.

*   Update the function IsOnionV3Address in `v3/util/onion.go`:
    *   Rename the existing function IsOnionV3 to IsOnionV3Address.
    *   Ensure it returns true for valid Tor V3 onion addresses, which must end with the .onion TLD, have a 56-character base32 label, and the last decoded byte equals 0x03.

*   Create a new function IsOnionV2Address in `v3/util/onion.go`:
    *   Return true if the DNS name ends with the .onion TLD, has at least two labels, and the leftmost label is exactly 16 characters of lowercase base32.
    *   Return false for non-onion names, V3 addresses, and names with no labels.

*   Implement package-level functions in `v3/util/onion.go`:
    *   allAreOnionVX: Accept a slice of strings and a predicate function, returning true if all strings satisfy the predicate. Return true for an empty slice.
    *   anyAreOnionVX: Accept a slice of strings and a predicate function, returning true if at least one string satisfies the predicate. Return false for an empty slice.

*   Add a new function IsOnionV2Cert in `v3/util/onion.go`:
    *   Return true if at least one of the certificate's DNS SANs or subject common name is a V2 onion address, using IsOnionV2Address.

*   Update the Tor service descriptor hash lint in `v3/lints/cabf_br/lint_ext_tor_service_descriptor_hash_invalid.go`:
    *   Ensure it returns lint.NA for certificates containing both a V3 onion address and regular DNS names.
    *   Apply the lint only when the certificate contains a V2 onion address.

*   Create and update test certificate files:
    *   Create `v3/testdata/onionV3AndDNS.pem` with a V3 Tor onion SAN and regular DNS SAN. Ensure lint returns lint.NA.
    *   Create `v3/testdata/invalidOnionAddress.pem` with SAN 'zmap.onion'. Ensure lint returns lint.Error with message '"zmap.onion" is not a v2 or v3 Tor address'.
    *   Update `v3/testdata/onionSANEV.pem` to include a valid V2 Tor onion address SAN (e.g., of3wk4tupf2ws33q.onion) and ensure it is EV-style.
    *   Update `v3/testdata/onionSANEVBefore201.pem` similarly to `onionSANEV.pem`, ensuring it retains a validity period before the CABF 2.0.1 effective date.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.