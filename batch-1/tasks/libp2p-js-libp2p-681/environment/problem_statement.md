## Description

Libp2p nodes need to be able to share and verify data — such as peer routing information — even when that data may be relayed through untrusted intermediaries. Currently, there is no built-in mechanism in js-libp2p to cryptographically sign and verify arbitrary records that are distributed across the network. This means it is impossible to prove that a piece of information (e.g. a set of listen addresses) genuinely originated from a particular peer and has not been tampered with.

## Expected Behavior

- There should be a general-purpose signed data container that can wrap any record with a cryptographic signature from the originating peer.
- It should be possible to "seal" a record into this container using a peer's private key, producing a serialized byte representation that can be sent over the wire.
- The container should be openable and verifiable by any recipient: given the raw bytes and the expected domain string, the recipient can confirm the record's authenticity. If the domain does not match, the operation should be rejected with an appropriate error.
- Two containers should be comparable for equality.
- There should be a concrete implementation of a peer routing record that stores a peer's listen addresses and a sequence number. This record should be serializable to and from bytes, comparable for equality (by peer identity, sequence number, and addresses), and compatible with the signed container.
- The error codes module should include a new code for invalid signatures.

## Why This Matters

Without verifiable records, nodes cannot distinguish authentic address announcements from tampered ones. This change lays the foundation for authenticated peer routing, enabling nodes to prioritize and trust self-certified address information.
