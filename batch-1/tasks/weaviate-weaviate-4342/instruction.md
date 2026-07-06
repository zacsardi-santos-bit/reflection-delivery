Implement two new functions, DotAVX512 and L2AVX512, in the assembly-optimized package to leverage the wider 512-bit vector instruction set for dot product and L2 squared distance calculations. Ensure these functions are only invoked on hardware that supports AVX-512 instructions and produce numerically accurate results across a range of vector sizes.

Requirements:

*   Implement DotAVX512 in the asm package at `adapters/repos/db/vector/hnsw/distancer/asm/`.
    *   Accept two `[]float32` parameters.
    *   Return a `float32` representing the dot product.
    *   Ensure numerical consistency with the existing asm.Dot (AVX) implementation for vector sizes 1 to 1536, including non-power-of-two sizes such as 1, 2, 3, 5, 6, 31, 67, 260, 299, 390, 777, and 784.
    *   Only execute on hardware with AVX-512 support.

*   Implement L2AVX512 in the asm package at `adapters/repos/db/vector/hnsw/distancer/asm/`.
    *   Accept two `[]float32` parameters.
    *   Return a `float32` representing the L2 squared distance.
    *   Produce results within a relative epsilon of 0.01 compared to a reference pure-Go L2 squared distance implementation for vector sizes 1 through 1536, including non-power-of-two sizes.
    *   Only execute on hardware with AVX-512 support.

*   Ensure both functions handle vectors of all tested sizes, including very small dimensions (1–6) and large embedding dimensions (up to 1536).

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.