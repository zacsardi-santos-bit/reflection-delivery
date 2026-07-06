I'm working on adding a sparse vector similarity index to our Rust-based index layer.

*   The MaxScoreWriter must accept (document_offset: u32, dimensions: Vec<(dimension_id: u32, weight: f32)>) pairs via a set() method and organize them per dimension into posting lists stored in a blockfile, with entries within each posting list sorted in ascending offset order.

*   The MaxScoreWriter must support a delete(offset: u32, dims: Vec<u32>) method that removes the specified dimensions from a document's posting list entries.

*   The MaxScoreWriter must support a with_block_size(u32) builder method to configure the maximum bytes per posting block; when a dimension's entries exceed this size, they must be split across multiple blocks.

*   The MaxScoreWriter must support forking from an existing MaxScoreReader via an optional second constructor argument; a fork writer must incorporate all existing entries from the reader and apply only the delta (adds/deletes/updates) on commit.

*   For dimensions with multiple blocks, the MaxScoreReader must store a per-dimension directory (under DIRECTORY_PREFIX key prefix) that records the maximum offset and maximum weight (max_offsets and max_weights) for each block, and a num_blocks count. Directory entries must be tagged as directory blocks (is_directory() == true); data block entries must not be.

*   The MaxScoreReader must expose a get_posting_blocks(key: &str) method (async) returning Vec<SparsePostingBlock> for a given encoded dimension key, and a get_directory(key: &str) method (async) returning Option of a directory object (with num_blocks(), max_offsets(), max_weights() methods) paired with a part count.

*   The MaxScoreReader must expose a get_all_dimension_ids() method (async) returning the list of dimension IDs stored in the index; an empty index must return an empty list.

*   The MaxScoreReader must expose a posting_id() method returning an identifier suitable for forking the underlying blockfile writer.

*   The MaxScoreReader must expose a query(query: Vec<(u32, f32)>, k: usize, mask: SignedRoaringBitmap) method (async) returning up to k results sorted by score descending, each with an offset: u32 and score: f32 field. When k is 0, or no documents match the query dimensions, or all matching documents are filtered by the mask, the result must be empty.

*   The query method must respect the SignedRoaringBitmap mask: an Include variant must restrict results to only offsets present in the bitmap; an Exclude variant must omit all offsets present in the bitmap. Results for large datasets (hundreds of docs, up to 10 dimensions) must achieve perfect recall accounting for f16 quantization ties at the boundary (scores within 5e-3 of the k-th result boundary are acceptable ties).

*   The PostingCursor must be constructable from a Vec<SparsePostingBlock> via PostingCursor::from_blocks(). Its advance(target: u32, mask: &SignedRoaringBitmap) method must return the first (offset, weight) pair where offset >= target that passes the mask, or None if exhausted. Its next() method must move past the current position.

*   PostingCursor::window_upper_bound(lo: u32, hi: u32) must return the maximum weight value across all entries whose offset is in [lo, hi] (inclusive).

*   PostingCursor::get_value(offset: u32) must return Some(weight) if an entry at exactly that offset exists, or None otherwise.

*   PostingCursor::drain_essential(lo: u32, hi: u32, query_weight: f32, accum: &mut Vec<f32>, bitmap: &mut [u64; 64], mask: &SignedRoaringBitmap) must process all entries with offset in [lo, hi] that pass the mask, adding weight * query_weight to accum[offset - lo] and setting bit (offset - lo) in the flat bitmap. Multiple calls with the same accum must accumulate (add) rather than overwrite.

*   PostingCursor::score_candidates(lo: u32, hi: u32, query_weight: f32, cand_docs: &[u32], cand_scores: &mut [f32]) must, for each candidate document offset in cand_docs that exists in the posting list within [lo, hi], add its weight * query_weight to the corresponding parallel slot in cand_scores. Candidate offsets not present in the posting list must leave their score slot unchanged.

*   SparsePostingBlock must be constructable from sorted (offset, weight) pairs via SparsePostingBlock::from_sorted_entries(entries: &[(u32, f32)]) and support a decode() method returning the stored offsets and weights as iterable sequences of u32 and f32 respectively, and an is_directory() -> bool method. The constant DIRECTORY_PREFIX (in chroma_types) must be a string prefix used to store directory entries separately from data blocks. The function encode_u32(dim: u32) -> String (in chroma_index::sparse::types) must encode a u32 dimension ID to a string key suitable for blockfile prefix lookups. The constant SPARSE_POSTING_BLOCK_SIZE_BYTES must be exposed from chroma_index::sparse::maxscore.


*   Interface details: Type: Constant
Name: SPARSE_POSTING_BLOCK_SIZE_BYTES
Location: rust/index/src/sparse/maxscore.rs (module path: chroma_index::sparse::maxscore)
Signature: SPARSE_POSTING_BLOCK_SIZE_BYTES: usize
Description: Default maximum size in bytes for a single posting block. Used when creating blockfile writers for the sparse posting list.

---

Type: Struct
Name: MaxScoreFlusher
Location: rust/index/src/sparse/maxscore.rs (module path: chroma_index::sparse::maxscore)
Description: Returned by MaxScoreWriter::commit(). Provides access to the posting ID and the flush operation.
Signature:
  flusher.id() -> Uuid
  flusher.flush() -> Result<(), E> [async]

---

Type: Struct
Name: MaxScoreWriter
Location: rust/index/src/sparse/maxscore.rs (module path: chroma_index::sparse::maxscore)
Description: Writes sparse posting lists into a blockfile. Supports both fresh builds and incremental forks from an existing MaxScoreReader. Must implement Clone.
Signature:
  MaxScoreWriter::new(posting_writer: BlockfileWriter, old_reader: Option<MaxScoreReader<'a>>) -> MaxScoreWriter<'a>
  writer.with_block_size(bs: u32) -> Self
  writer.set(offset: u32, dims: impl IntoIterator<Item=(u32, f32)>) -> () [async]
  writer.delete(offset: u32, dims: impl IntoIterator<Item=u32>) -> () [async]
  writer.commit() -> Result<MaxScoreFlusher, E> [async]

---

Type: Struct
Name: MaxScoreReader
Location: rust/index/src/sparse/maxscore.rs (module path: chroma_index::sparse::maxscore)
Description: Reads and queries sparse posting lists from a blockfile. Must implement Clone.
Signature:
  MaxScoreReader::new(posting_reader: BlockfileReader<u32, SparsePostingBlock>) -> MaxScoreReader<'a>
  reader.clone() -> MaxScoreReader<'a>  [Clone]
  reader.posting_id() -> Uuid
  reader.posting_reader() -> &BlockfileReader<u32, SparsePostingBlock>
    (used as: reader.posting_reader().get_prefix(key: &str) [async] -> iterator of (String/&str, SparsePostingBlock) pairs)
  reader.get_posting_blocks(encoded_dim: &str) -> Result<Vec<SparsePostingBlock>, E> [async]
  reader.get_directory(encoded_dim: &str) -> Result<Option<(D, usize)>, E> [async]
    where D satisfies:
      .num_blocks() -> usize
      .max_offsets() -> &[u32]  (or something indexable yielding u32)
      .max_weights() -> &[f32]  (or something indexable yielding f32)
  reader.get_all_dimension_ids() -> Result<Vec<u32>, E> [async]
  reader.query(query: impl IntoIterator<Item=(u32, f32)>, k: u32, mask: SignedRoaringBitmap) -> Result<Vec<Score>, E> [async]
    -- Returns up to k results sorted by score descending.
    -- Score is defined in chroma_index::sparse::types (see Score type below).

---

Type: Struct
Name: Score
Location: rust/index/src/sparse/types.rs (module path: chroma_index::sparse::types)
Description: The query result type returned by MaxScoreReader::query(). Must have exactly these two public fields: score (f32) and offset (u32). Tests access them as r.score and r.offset.
Signature:
  pub struct Score {
      pub score: f32,
      pub offset: u32,
  }

---

Type: Struct
Name: PostingCursor
Location: rust/index/src/sparse/maxscore.rs (module path: chroma_index::sparse::maxscore)
Description: A cursor over a sequence of SparsePostingBlock values for a single dimension. Used during query evaluation for advancing, scoring, and accumulating.
Signature:
  PostingCursor::from_blocks(blocks: Vec<SparsePostingBlock>) -> PostingCursor
  cursor.advance(target: u32, mask: &SignedRoaringBitmap) -> Option<(u32, f32)>
    -- Returns the first (offset, weight) where offset >= target and the offset passes the mask
       (Include variant: offset must be in the bitmap; Exclude variant: offset must NOT be in the bitmap).
       Returns None when exhausted.
  cursor.next()
    -- Advances past the current entry.
  cursor.window_upper_bound(lo: u32, hi: u32) -> f32
    -- Returns the maximum weight across all entries with offset in [lo, hi] inclusive.
  cursor.get_value(offset: u32) -> Option<f32>
    -- Returns the stored weight for exactly this offset, or None if not present.
  cursor.drain_essential(lo: u32, hi: u32, query_weight: f32, accum: &mut [f32], bitmap: &mut [u64], mask: &SignedRoaringBitmap)
    -- For each entry with offset in [lo, hi] (inclusive) that passes the mask:
         accum[offset - lo] += weight * query_weight
         Sets bit (offset - lo) in the flat u64 bitmap: bitmap[(offset-lo)/64] |= 1 << ((offset-lo)%64)
       Multiple calls accumulate additively into the same accum/bitmap arrays.
       Tests call this with accum = vec![0.0f32; 4096] (coerced to &mut [f32])
       and bitmap = [0u64; 64] (coerced to &mut [u64]).
  cursor.score_candidates(lo: u32, hi: u32, query_weight: f32, cand_docs: &[u32], cand_scores: &mut [f32])
    -- For each index i where cand_docs[i] is in [lo, hi] and exists in the posting list:
         cand_scores[i] += weight_of(cand_docs[i]) * query_weight
       Candidate offsets not in the posting list leave their score slot unchanged.

---

NOTE on pre-existing types used in tests:
The following types and functions already exist in the codebase and must be used (not redefined):
- chroma_types::SparsePostingBlock — blockfile value type; has from_sorted_entries(&[(u32,f32)]) -> Result, decode() -> (offsets, values), is_directory() -> bool
- chroma_types::DIRECTORY_PREFIX — &str constant used as key prefix for directory entries
- chroma_types::Directory — has num_blocks(), max_offsets() -> &[u32], max_weights() -> &[f32]
- chroma_types::DirectoryBlock — used for parsing directory parts
- chroma_index::sparse::types::encode_u32(dim: u32) -> String — encodes a dimension ID for blockfile keys
- chroma_index::sparse::types::decode_u32(s: &str) -> Result<u32, _> — decodes an encoded dimension ID


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.