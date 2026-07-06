## Description

We need a new sparse vector similarity index in the index layer. Currently, the system has no built-in support for efficiently searching documents represented as sparse weighted vectors — where each document has non-zero weights for only a subset of dimensions. This is a common representation in many information retrieval and search scenarios.

## Expected Behavior

The new index should support:

- **Building an index** from a collection of documents, each described as a list of (dimension, weight) pairs, organized internally into per-dimension posting lists stored in a block-based file format.
- **Incremental updates**: the ability to fork an existing index snapshot, apply a set of additions, deletions, and weight updates to individual documents, and commit the result as a new snapshot — without rewriting unaffected parts of the index.
- **Top-k similarity queries** over the index, given a sparse query vector, returning the highest-scoring documents with their scores. Results must be sorted by score in descending order.
- **Document filters**: queries must support an allow-list mode (only consider specified documents) and a deny-list mode (exclude specified documents) via a signed bitmap structure.
- **Per-dimension directory metadata**: for dimensions split across multiple blocks, a directory must be maintained recording the maximum offset and maximum weight per block, enabling score-based pruning.
- **Cursor-based scoring primitives** that allow iterating over posting list blocks, advancing to a target offset with mask filtering, computing the maximum weight in an offset window, accumulating scores into a flat array, and scoring a specific set of candidate documents.

## Why This Matters

Many retrieval workloads use sparse representations. Without this index, callers would need to scan all documents on every query, which does not scale. The block-based layout with a per-block directory also enables efficient maximum-score pruning, skipping entire blocks when their maximum contribution cannot improve the current top-k heap.

## Notes

- Weights are stored as 16-bit floats for compactness; query result scores may differ from exact 32-bit arithmetic by a small amount at tied boundaries.
- The suffix-rewrite optimization means that when modifying entries in a forked index, only the blocks at and after the first affected block for each dimension need to be rewritten; earlier blocks are carried over unchanged.
