Implement gas cost calculations for memory and storage operations in the EVM emulator. Ensure accurate gas accounting for memory expansion and storage access, distinguishing between cold and warm accesses.

*   Update the `Memory` struct in `common/src/ether/evm/core/memory.rs`:
    *   Implement `memory_cost(&self) -> u128` to compute the current gas cost based on allocated memory size. Use the formula: `let memory_word_size = (self.size() + 31) / 32; (memory_word_size.pow(2)) / 512 + (3 * memory_word_size)`.
    *   Implement `expansion_cost(&self, offset: usize, size: usize) -> u128` to calculate the incremental gas cost for expanding memory to cover a new region. Return zero if no expansion is needed.

*   Update the `Storage` struct in `common/src/ether/evm/core/storage.rs`:
    *   Implement `access_cost(&mut self, key: [u8; 32]) -> u128` to return the gas cost for accessing a storage slot. Return 2100 for a cold key and 100 for a warm key, marking the key as warm after access.
    *   Implement `storage_cost(&mut self, key: [u8; 32], value: [u8; 32]) -> u128` to compute the full gas cost of writing to a storage slot. Use the base costs: 20000 for non-zero values and 2900 for zero values, adding the result of `access_cost(key)`.
    *   Extend the `Storage` struct to track accessed keys, marking them as warm after a load or store operation. Modify the `load` method to `&mut self` to update the access tracking.

*   Update the EVM core module path to ensure all components are accessible under `crate::ether::evm::core::{memory,storage,vm,opcodes,stack,types}`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.