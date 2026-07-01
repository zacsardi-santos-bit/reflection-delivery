I'm tracking down a bug in the dynamic commitment message types in the wire protocol package. The issue is that when I take a valid raw binary message, decode it into a struct, and then re-encode it, I don't get the same bytes back. The round-trip is broken.

After digging in, it looks like there are a couple of problems. First, known TLV fields are being written into the extra/opaque data portion of the message instead of into their proper dedicated TLV slots — or worse, they're being written to both places, causing duplication in the output. Second, some satoshi-denominated fields are using fixed-width integer encoding when they should be using variable-length encoding, so the encoded values don't match the expected on-wire format.

There's also a constant that identifies the musig2 public nonce TLV record which appears to have the wrong type number and needs to be corrected.

The fix needs to ensure that: (1) each known field is encoded exactly once in its own TLV slot, (2) unknown TLV records from the input are preserved and re-emitted verbatim, (3) satoshi-valued fields use the correct variable-length encoding, and (4) the nonce TLV type constant has the correct value. It may be helpful to introduce a shared helper that parses known records from a raw TLV byte stream while collecting the remaining unrecognized records separately for storage as extra data.
