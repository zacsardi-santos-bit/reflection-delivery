Refactor the autoscaling boost module to generalize its functionality beyond cluster-level autoscaling. Update the module to accept a pre-computed Zookeeper path and decouple the command-line interface logic from the business logic.

*   Rename the module:
    *   Change the module name from `cluster_boost` to `load_boost`.
    *   Update the file path to `paasta_tools/autoscaling/load_boost.py`.
    *   Update all imports in `paasta_cluster_boost.py` to reflect this change.

*   Modify function signatures in `load_boost`:
    *   Implement `get_zk_cluster_boost_path(region: str, pool: str) -> str` to return a Zookeeper path formatted as `'/paasta_cluster_autoscaler/{region}/{pool}/boost'`.
    *   Update `get_boost_values(zk_boost_path: str, zk: KazooClient) -> BoostValues` to accept `zk_boost_path` as the first parameter. Return `BoostValues(end_time=0, boost_factor=1.0, expected_load=0)` if no data exists.
    *   Update `set_boost_factor(zk_boost_path: str, region: str='', pool: str='', send_clusterman_metrics: bool=False, factor: float=DEFAULT_BOOST_FACTOR, duration_minutes: int=DEFAULT_BOOST_DURATION, override: bool=False) -> bool`. Return `False` if a boost is active and `override` is `False`; otherwise, return `True`.
    *   Update `clear_boost(zk_boost_path: str) -> bool` to accept `zk_boost_path`.
    *   Update `get_boost_factor(zk_boost_path: str) -> float` to accept `zk_boost_path`.

*   Ensure constants and classes are accessible:
    *   Keep `DEFAULT_BOOST_FACTOR`, `DEFAULT_BOOST_DURATION`, `BoostValues`, and `NoNodeError` accessible from `load_boost`.

*   Refactor `paasta_cluster_boost` in `paasta_tools/paasta_cluster_boost.py`:
    *   Change the function signature to `paasta_cluster_boost(action: str, pool: str, boost: float, duration: int, override: bool) -> bool`.
    *   Return `False` if cluster boost is not enabled or `get_regions()` returns an empty list.
    *   For `action='set'`, call `load_boost.set_boost_factor` with `zk_boost_path`, `region`, `pool`, `send_clusterman_metrics=True`, `factor=boost`, `duration_minutes=duration`, `override=override`.
    *   For `action='clear'`, call `load_boost.clear_boost(zk_boost_path=...)`.
    *   For `action='status'`, return `True` without calling `set_boost_factor` or `clear_boost`.

*   Implement a new entry point function `main()` in `paasta_tools/paasta_cluster_boost.py`:
    *   Call `parse_args()`, configure logging based on `args.verbose`, and invoke `paasta_cluster_boost()` with parsed arguments.
    *   Exit with `sys.exit(0)` if the result is `True` or `sys.exit(1)` if `False`.

*   Update the `if __name__ == '__main__'` block in `paasta_cluster_boost.py` to call `main()`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.