Move the `FeeStore` class to the correct module and update error handling in the mempool. Ensure the `FeeStore` is correctly importable from the protocols module, and modify the mempool manager to return the appropriate error code for non-canonical coin solutions.

*   Relocate the `FeeStore` class:
    *   Move `FeeStore` from `chia/full_node/fee_estimate_store.py` to `chia/protocols/fee_estimate_store.py`.
    *   Ensure `FeeStore` is importable using `from chia.protocols.fee_estimate_store import FeeStore`.
    *   Update all internal imports of `FeeStore` in files such as `chia/full_node/bitcoin_fee_estimator.py` and `chia/full_node/fee_tracker.py` to reflect the new import path.

*   Update error handling in `MempoolManager`:
    *   Modify `MempoolManager.add_spend_bundle` to handle spend bundles with non-canonically encoded solutions.
    *   Ensure that when a dedup-eligible coin spend has a non-canonical CLVM encoding, the method returns a result with:
        *   `status == MempoolInclusionStatus.FAILED`
        *   `error == Err.INVALID_COIN_SOLUTION`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.