Implement a general-purpose signed data container to cryptographically sign and verify records distributed across the network. Create a concrete implementation for peer routing records that can be serialized, compared, and verified. Update error codes to handle invalid signatures.

*   Implement the `Envelope` class in `src/record/envelope/index.js` or `src/record/envelope.js`:
    *   Constructor: Accepts an object with `peerId`, `payloadType` (Buffer), `payload` (Buffer), and `signature` (Buffer). Store these as instance properties.
    *   `marshal()`: Serialize the envelope to a Buffer using protobuf encoding.
    *   `equals(other)`: Return true if the envelopes have identical `peerId` public key bytes, `payloadType`, `payload`, and `signature`.
    *   `static async seal(record, peerId)`: Marshal the record's payload, sign it with the peer's private key, and return a new `Envelope` instance.
    *   `static async openAndCertify(data, domain)`: Deserialize the envelope from a Buffer and validate its signature against the domain string. Throw an error with code 'ERR_SIGNATURE_NOT_VALID' if validation fails.

*   Implement the `PeerRecord` class in `src/record/peer-record/index.js` or `src/record/peer-record.js`:
    *   Extend `Record` from `libp2p-interfaces/src/record`.
    *   Constructor: Accept an object with `peerId` (required), `multiaddrs` (optional, defaults to empty array), and `seqNumber` (optional, defaults to `Date.now()`). Store these as instance properties.
    *   `marshal()`: Serialize the peer record to a Buffer.
    *   `equals(other)`: Return true if `peerId`, `seqNumber`, and `multiaddrs` are all equal; otherwise, return false.
    *   `static createFromProtobuf(buf)`: Deserialize a peer record from a protobuf-encoded Buffer and return a new `PeerRecord` instance.
    *   Ensure the `domain` property is set via the `Record` base class constructor for use in `Envelope.openAndCertify()`.

*   Update error codes in `src/errors.js`:
    *   Include `ERR_SIGNATURE_NOT_VALID` with the value 'ERR_SIGNATURE_NOT_VALID'.

*   Ensure a sealed `PeerRecord` envelope can be sealed, marshaled, opened with `openAndCertify`, and extracted via `createFromProtobuf` to produce a record equal to the original.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.