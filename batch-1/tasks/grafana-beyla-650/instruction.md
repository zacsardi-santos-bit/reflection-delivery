Remove the "mark as duplicate" mode from the network flow deduplication system, ensuring that duplicates are always dropped. Clear interface and direction fields to sentinel values after deduplication to indicate they are unset.

*   Update the Deduper struct:
    *   Ensure it only contains the fields: Type (string) and ExpireTime (time.Duration).
    *   Remove the JustMark field to prevent its use during initialization.

*   Modify DeduperProvider:
    *   Ensure it only drops duplicate network flow records.
    *   Set non-duplicate records' Id.IfIndex to ebpf.InterfaceUnset and Id.Direction to ebpf.DirectionUnset before forwarding.

*   Define constants in the ebpf package:
    *   InterfaceUnset with a value of 0xFFFFFFFF to indicate an unset interface index.
    *   DirectionUnset with a value of 0xFF to indicate an unset direction field.

*   Update the Record struct in the ebpf package:
    *   Remove the Duplicate bool field.

*   Update the NetFlowIdT struct in specified files:
    *   Remove the SrcMac [6]uint8 and DstMac [6]uint8 fields.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.