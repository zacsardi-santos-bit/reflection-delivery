Refactor the `generateHashSides` function to improve its usability and testability by changing its parameters and handling errors appropriately. Implement the function to compute sibling hash directions for Merkle tree proofs based on given leaf position and total leaf count.

*   Update the `generateHashSides` function to accept two integer parameters:
    *   `nodePosition` (uint64): The zero-indexed position of the leaf within the tree.
    *   `breadth` (uint64): The total number of leaves in the tree.
*   Ensure the function returns:
    *   A slice of booleans representing sibling-hash side directions.
    *   An error value, which is nil for valid inputs.
*   Handle specific cases:
    *   For a single-leaf tree (e.g., `nodePosition` 0, `breadth` 1), return an empty boolean slice and a nil error.
    *   For multi-leaf trees, compute and return the correct sibling-hash side directions for the given leaf position.
        *   Example: For position 3 of 6 leaves, return `[true, true, false]`.
        *   Example: For position 4 of 6 leaves, return `[false, true]`.
        *   Example: For position 4 of 8 leaves, return `[false, false, true]`.
*   Implement error handling:
    *   If `nodePosition` is greater than or equal to `breadth`, return `nil` and an error.
    *   The error message must be formatted as: "leaf position %v is too high in proof with %v leaves", substituting the actual `nodePosition` and `breadth` values.
*   Ensure the function signature is:
    *   `generateHashSides(nodePosition uint64, breadth uint64) ([]bool, error)`
*   Place the function in the file: `relayer/relays/parachain/types.go`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.