I'm trying to use Verus to formally verify code that uses standard Rust hash maps, but there's no support for reasoning about hash maps in the verification library. When I try to assert things like "after inserting a key, the map contains that key" or "looking up a key returns the value I inserted", the verifier can't prove these facts because there are no specifications for hash map operations.

I also need to reason about the standard hash function — specifically, I'd like to track what data has been written to a hasher and prove that two hashers given the same sequence of writes produce the same output.

For maps with primitive keys this should ideally work automatically. For maps with custom key types, I understand I may need to explicitly state an assumption that the key type's hash and equality behavior is well-behaved. If that assumption is missing, the verifier should correctly reject the proof attempt.

I'd also like support for two specialized cases: a map wrapper for keys that carry a logical view (so I can write proofs in terms of the abstract key rather than the concrete key), and a dedicated map type for string keys that represents each string as a sequence of characters in the abstract.
