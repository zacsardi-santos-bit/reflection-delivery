I'm working on a Rust blockchain node that verifies randomness beacon entries from drand networks. Currently, the code only handles the "chained" beacon scheme used by the traditional mainnet drand network, where each beacon entry's signed message is formed by hashing the previous entry's signature together with the current round number. But there's a newer drand network (sometimes called "quicknet") that uses an "unchained" scheme — each entry signs only the current round number independently, with no dependency on a previous entry.

These two schemes also differ in which elliptic curve groups they use for public keys and signatures — one scheme places the public key on one group and signatures on the other, while the newer unchained scheme reverses that arrangement. Right now the codebase conflates these two layouts and has no clean abstraction for either.

I'd like to introduce a dedicated beacon signatures module that cleanly separates both schemes. Specifically:

- The unchained variant needs its own public key type (for the curve group it uses) with both single-entry and batch verification methods
- The chained variant needs a standalone verification function that accepts a public key, a slice of pre-hashed messages, and a slice of signatures
- The beacon entry type should gain two new helper methods: one that computes the message hash for the unchained scheme (just the current round), and one for the chained scheme (the previous signature bytes followed by the current round)

The implementations should be verified against real public keys and signatures from the live drand API for both networks, covering both successful verification of correct signatures and rejection of incorrect ones.
