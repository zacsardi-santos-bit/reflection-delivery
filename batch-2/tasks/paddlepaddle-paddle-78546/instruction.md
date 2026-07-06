I'm working on a model that uses gradient checkpointing together with pipeline parallelism, and I'm hitting a crash during the backward pass.

*   The _restore_freed_closure_tensors function must be importable by name from paddle.distributed.fleet.recompute.recompute.

*   When _restore_freed_closure_tensors is called with a context whose closure_cells or closure_protected entries are None, those pairs must be silently skipped without raising any exception.

*   When _restore_freed_closure_tensors encounters a cell whose cell_contents raises ValueError (empty cell), it must silently skip that entry without raising any exception.

*   When _restore_freed_closure_tensors encounters a cell whose contents is an already-initialized tensor (_is_initialized() returns True), it must leave the cell unchanged.

*   When _restore_freed_closure_tensors encounters a cell whose contents is a non-tensor value, it must leave the cell unchanged.

*   When _restore_freed_closure_tensors encounters a cell whose tensor contents is uninitialized (_is_initialized() returns False, indicating it was freed via _clear_dataptr()), it must replace that cell's content with the corresponding protected copy using ctypes PyCell_Set. After restoration, the cell must hold a core.eager.Tensor whose numpy data matches the original tensor's data.

*   When processing a mixed list of cells (None entries, freed cells, and normal initialized cells), _restore_freed_closure_tensors must patch only the freed cells; all other cells must remain unchanged.

*   RecomputeFunction.forward() must scan the closure of the recomputed function: for nn.Layer instances it uses the forward method's __closure__, for plain functions it uses __closure__ directly. For each cell containing a tensor, the cell object is stored in ctx.closure_cells and a _new_shared_tensor() copy is stored in ctx.closure_protected. For non-tensor values, empty cells (ValueError), or absent closures, None must be appended to both lists.

*   RecomputeFunction.backward() must call _restore_freed_closure_tensors(ctx) before re-running the forward pass. This must occur in both the preserve_rng_state=True and preserve_rng_state=False code branches.

*   The recompute() public API must complete forward and backward without error for functions with no closure, closures containing only non-tensor values, closures containing tensors, and closures containing empty (freed) cells.

*   When a closure-captured tensor is freed via _clear_dataptr() after forward but before backward, recompute()'s backward must succeed and produce an initialized, non-None gradient for the input tensor.

*   Gradients computed via recompute() on a function with a closure tensor must numerically match gradients computed without recompute (within rtol=1e-5).

*   The recompute() API must support use_reentrant=False and preserve_rng_state=False flags while correctly handling closure tensors.


*   Interface details: Type: Function
Name: _restore_freed_closure_tensors
Location: python/paddle/distributed/fleet/recompute/recompute.py
Signature: _restore_freed_closure_tensors(ctx) -> None
Description: Restores closure-captured tensors that were freed (data pointer cleared) between forward and backward passes. Iterates over parallel lists ctx.closure_cells and ctx.closure_protected. For each pair: silently skips if either is None; silently skips if cell.cell_contents raises ValueError (empty cell); silently skips if the cell's value is not a tensor or is already initialized (_is_initialized() returns True); replaces the cell's content with the protected copy (using ctypes PyCell_Set) if the cell holds an uninitialized tensor (_is_initialized() returns False). After restore, the patched cell must hold a core.eager.Tensor with data matching the original.

Type: Class
Name: RecomputeFunction
Location: python/paddle/distributed/fleet/recompute/recompute.py
Description: PyLayer subclass that implements gradient checkpointing. Its forward() method must scan run_function.__closure__ (or run_function.forward.__closure__ for nn.Layer instances) and populate ctx.closure_cells (list of CPython cell objects or None) and ctx.closure_protected (list of _new_shared_tensor() copies or None). For tensor-holding cells, the actual cell object goes into ctx.closure_cells and a _new_shared_tensor() copy goes into ctx.closure_protected. For non-tensor, empty (ValueError), or absent closures, None is appended to both lists. Its backward() method must call _restore_freed_closure_tensors(ctx) before re-running the forward pass, in both the preserve_rng_state=True and preserve_rng_state=False branches.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.