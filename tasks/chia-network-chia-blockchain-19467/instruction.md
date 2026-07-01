Migrate the peer address manager from a text-based serialization format to a binary format while ensuring backward compatibility. Implement methods to serialize peer data to binary and load from both binary and text-based formats. Handle missing files and malformed entries gracefully.

* Implement `serialize_bytes(self) -> bytes` in `chia/server/address_manager.py`:
    * Serialize peer data to a binary format including: 32-byte key (big-endian), uint64 new-node count, uint32 new-table entry count, new-table entries (each uint64 unique_id then uint64 bucket), followed by node records.
    * Node records must be serialized using `ExtendedPeerInfo.stream()` in the order: IP type byte, packed IP bytes, uint16 port, uint64 timestamp, source IP type byte, packed source IP bytes, uint16 source port.

* Implement `create_address_manager(peers_file_path: Path) -> AddressManager` in `chia/server/address_manager.py`:
    * Load an `AddressManager` from a file supporting both binary and text-based formats.
    * Return a new empty `AddressManager` if the file does not exist.
    * Skip entries with unrecognized IP type bytes in binary data, returning an `AddressManager` with valid entries only.

* Ensure `BUCKET_SIZE` and `NEW_BUCKET_COUNT` are exported at the module level in `chia/server/address_manager.py`.

* Implement `encode_ip_type(self_or_cls, ip) -> bytes` in `chia/server/address_manager.py`:
    * Return b'\x00' for IPv4 and b'\x01' for IPv6.

* Implement `stream(self, out: io.BytesIO) -> None` in `chia/server/address_manager.py`:
    * Write peer record to the buffer in the format: IP type byte, packed peer IP, uint16 port, uint64 timestamp, source IP type byte, packed source IP, uint16 source port.

* Ensure `PeerDataSerialization` remains importable from `chia/server/address_manager_store.py`:
    * Accepts (metadata, nodes, new_table_entries) arguments.
    * Calling `bytes()` on an instance must produce serialized bytes of the old format.

* Ensure round-trip serialization is lossless:
    * Serializing with `serialize_bytes` and reloading with `create_address_manager` must recover all peers with matching peer_info, src, and timestamp fields.

* Correctly handle IPv6 addresses through serialization and deserialization, preserving peer_info, src, and timestamp.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.