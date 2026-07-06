Fix the encoding and decoding logic for dynamic commitment message types in the Lightning Network wire protocol package. Ensure that known TLV fields are correctly serialized, satoshi-denominated values use variable-length encoding, and the TLV type for the LocalNonce field is corrected.

*   Implement strict round-trip invariants for DynPropose, DynAck, and DynCommit:
    *   Ensure that decoding raw bytes into a message struct and re-encoding them produces identical byte output.
*   Correct serialization of DustLimit and ChannelReserve fields:
    *   Use BigSize variable-length encoding for these fields in DynPropose and DynCommit.
    *   Ensure decoding and re-encoding BigSize-encoded values reproduces the same bytes.
*   Properly handle TLV fields during encoding:
    *   Known TLV fields (DustLimit, MaxValueInFlight, HtlcMinimum, ChannelReserve, CsvDelay, MaxAcceptedHTLCs, ChannelType, LocalNonce) must not appear in the ExtraData blob.
    *   Encode each known field exactly once as its own TLV record.
    *   Preserve unknown TLV records in the ExtraData field and re-emit them verbatim during re-encoding.
*   Correct the TLV type constant for the LocalNonce field:
    *   Set the TLV type for LocalNonce in DynAck and DynCommit to 20 (decimal).
*   Implement the function `ParseAndExtractExtraData` in `lnwire/extra_bytes.go`:
    *   Parse a raw TLV byte slice into known record slots.
    *   Return a set of TLV types found and a new ExtraOpaqueData containing only unknown records.
    *   Signature: `ParseAndExtractExtraData(allTlvData ExtraOpaqueData, knownRecords ...tlv.RecordProducer) (fn.Set[tlv.Type], ExtraOpaqueData, error)`
*   Ensure correct wire format for each message type:
    *   DynPropose: 32-byte ChannelID + TLV stream.
    *   DynAck: 32-byte ChannelID + 64-byte Sig + TLV stream.
    *   DynCommit: 32-byte ChannelID + 64-byte Sig + TLV stream.
    *   TLV streams must be in ascending TLV type order.
*   Update the constant `DALocalMusig2Pubnonce` in `lnwire/dyn_ack.go` to have a value of 20.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.