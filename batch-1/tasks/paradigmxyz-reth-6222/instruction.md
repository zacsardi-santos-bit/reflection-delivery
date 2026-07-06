Implement a validation layer for transaction announcements in an Ethereum node. This layer should inspect each entry in an announcement to decide whether to fetch it, ignore it, or flag the announcing peer for bad behavior. Ensure compatibility with both older and newer announcement formats and provide a default implementation for standard Ethereum transaction types.

*   Define the `FilterAnnouncement` trait with:
    *   `filter_valid_entries_68(&self, msg: NewPooledTransactionHashes68) -> (FilterOutcome, ValidAnnouncementData)` where `Self: ValidateTx68`.
    *   `filter_valid_entries_66(&self, msg: NewPooledTransactionHashes66) -> (FilterOutcome, ValidAnnouncementData)`.

*   Implement `filter_valid_entries_68` to:
    *   Return `(FilterOutcome::ReportPeer, HashMap::new())` for empty announcements.
    *   Filter out unrecognized transaction types and return `FilterOutcome::ReportPeer`.
    *   Filter out entries with encoded size 0 without penalizing the peer.
    *   Deduplicate duplicate transaction hashes and return `FilterOutcome::ReportPeer`.

*   Implement `filter_valid_entries_66` to:
    *   Return `(FilterOutcome::ReportPeer, HashMap::new())` for empty announcements.
    *   Deduplicate duplicate transaction hashes and return `FilterOutcome::ReportPeer`.

*   Define `ValidAnnouncementData` as `HashMap<TxHash, Option<(u8, usize)>>`.
    *   For eth68, map values are `Some((tx_type_u8, encoded_size))`.
    *   For eth66, map values are `None`.

*   Define `FilterOutcome` enum with variants `Ok` and `ReportPeer`.

*   Define `ValidationOutcome` enum with variants `Fetch`, `Ignore`, and `ReportPeer`.

*   Implement `EthAnnouncementFilter` as a zero-sized type that:
    *   Implements `ValidateTx68` and `FilterAnnouncement`.
    *   Implements `Display` to return `"EthAnnouncementFilter"`.

*   Implement `AnnouncementFilter` as a generic wrapper struct:
    *   Defaults to `EthAnnouncementFilter`.
    *   Implements `Deref` and `DerefMut` to the inner type.

*   Ensure `TxType` exposes `MAX_RESERVED_EIP` and implements `TryFrom<u8>`.

*   Define `ValidateTx68` trait with:
    *   `should_fetch(&self, ty: u8, hash: TxHash, size: usize) -> ValidationOutcome`.
    *   `max_encoded_tx_length(&self, ty: TxType) -> Option<usize>`.
    *   `strict_max_encoded_tx_length(&self, ty: TxType) -> Option<usize>`.
    *   `min_encoded_tx_length(&self, ty: TxType) -> Option<usize>`.
    *   `strict_min_encoded_tx_length(&self, ty: TxType) -> Option<usize>`.

*   Define all types in `crates/net/network/src/transactions/validation.rs` and re-export them from `crates/net/network/src/transactions/mod.rs`.

*   Declare `mod validation;` in `crates/net/network/src/transactions/mod.rs` and re-export with `pub use validation::*;`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.