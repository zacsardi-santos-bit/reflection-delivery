## Description

There is a bug in the encoding and decoding logic of the dynamic commitment message types. Known TLV fields (such as dust limits, in-flight value caps, HTLC minimums, reserve amounts, and timing delays) are being incorrectly serialized into the extra/opaque data portion of the message instead of (or in addition to) their proper places in the TLV stream. This causes the messages to fail a round-trip test: if you decode a raw byte sequence into a message struct and then re-encode it, the resulting bytes do not match the original input.

There is also a related issue with how satoshi-denominated values are encoded. These values must use variable-length encoding rather than fixed-width integers, but the current code uses the wrong type, causing the encoded byte representation to differ from what a correctly-formatted message would contain.

## Expected Behavior

- Decoding a raw binary dynamic commitment message and then re-encoding it must produce the exact same bytes.
- Each known TLV field must appear exactly once in the encoded output — in its designated TLV slot, not duplicated in the extra-data blob.
- Unknown/future TLV records that are not recognized by the current code must be passed through verbatim (preserved in the extra-data field) so that forward compatibility is maintained.
- Satoshi-denominated fields (dust limit and channel reserve) must use variable-length encoding so that the encoded bytes match the on-wire format that other implementations produce.
- The TLV type number used for the local musig2 nonce field must be corrected to the proper value.

## Why This Matters

The dynamic commitment protocol allows two channel peers to negotiate updated channel parameters without closing and reopening their channel. If the encoding of these negotiation messages is incorrect, the resulting byte streams will not match what peers expect, breaking interoperability and making it impossible to safely upgrade channel parameters in production.
