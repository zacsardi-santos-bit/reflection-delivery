I'm seeing an issue with the GPU profiling manager in our Ray cluster.

*   GpuProfilingManager.node_has_gpus() must call subprocess.check_output with exactly the argument list ["nvidia-smi", "--query-gpu=name", "--format=csv,noheader"] when detecting whether the node has GPUs.

*   GpuProfilingManager.node_has_gpus() must return True when subprocess.check_output succeeds (returns bytes without raising an exception), and False when any exception is raised.

*   GpuProfilingManager.node_has_gpus() must be a cached method decorated with @functools.cache, exposing a cache_clear() callable on it.

*   The enabled property of GpuProfilingManager must check for the presence of dynolog binaries (_dynolog_bin and _dyno_bin) before calling node_has_gpus(). If either dynolog binary is absent, enabled must return False without invoking node_has_gpus() at all.


*   Interface details: Type: Class
Name: GpuProfilingManager
Location: python/ray/dashboard/modules/reporter/gpu_profile_manager.py
Description: Manages GPU profiling using dynolog. Responsible for detecting GPU presence and controlling a dynolog daemon.
Signature: __init__(self, profile_dir_path, *, ip_address: str) -> None

Type: Method
Name: node_has_gpus
Location: python/ray/dashboard/modules/reporter/gpu_profile_manager.py
Signature: node_has_gpus(cls) -> bool
Description: Cached classmethod (decorated with @functools.cache, exposing cache_clear()) that checks whether the current node has NVIDIA GPUs. Must invoke subprocess.check_output with the argument list ["nvidia-smi", "--query-gpu=name", "--format=csv,noheader"]. Returns True if the command succeeds without raising an exception, False otherwise.

Type: Property
Name: enabled
Location: python/ray/dashboard/modules/reporter/gpu_profile_manager.py
Signature: enabled -> bool
Description: Returns True only when both dynolog binaries (_dynolog_bin and _dyno_bin) are present AND node_has_gpus() returns True. Must check for dynolog binaries FIRST; if either binary is absent, must return False without calling node_has_gpus().


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.