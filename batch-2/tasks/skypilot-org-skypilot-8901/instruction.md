I'm working on improving GPU resource matching for Slurm clusters.

*   The CANONICAL_GPU_NAMES constant must be moved to sky/utils/gpu_names.py so it is importable as 'from sky.utils import gpu_names' and accessible as 'gpu_names.CANONICAL_GPU_NAMES'. The Kubernetes GPU label formatter tests must import it from this new location.

*   CANONICAL_GPU_NAMES must be ordered so that more specific names appear before shorter prefix-equivalent names: 'L40S' must come before 'L40', which must come before 'L4'; modern GPUs including B200, H100, H200, GH200, A100, A10, V100 must be present.

*   The _normalize_gpu_name function in sky/provision/slurm/utils.py must strip leading 'nvidia_' and 'tesla_' vendor prefixes (case-insensitive), convert all remaining underscores to dashes, and lowercase the result. 'RTX' must NOT be treated as a vendor prefix. The 'amd_' prefix must also be stripped. Mixed separator strings (e.g. 'nvidia_a100-sxm4-80gb') must be handled by stripping only the vendor prefix and preserving remaining characters.

*   The _accelerator_name_matches_slurm function in sky/provision/slurm/utils.py must normalize both inputs and use subsequence token matching. It must return True when the normalized tokens of 'requested' appear as a subsequence of the normalized tokens of 'slurm_gres', and False otherwise. Critically: 'L4' must not match 'L40'; 'RTXA6000' (dashless) must not match 'nvidia_rtx_a6000'; 'RTX-A6000' (dash form) must match 'nvidia_rtx_a6000'; 'A100-80GB' must not match 'nvidia_a100_pcie_40gb'; 'GH200' must not match 'nvidia_h100_sxm'.

*   The resolve_gres_gpu_type function in sky/provision/slurm/utils.py must accept (cluster: str, requested: str, count: int, partition: str = None) and return the raw GRES GPU type string. It must raise sky.exceptions.ResourcesUnavailableError when no match is found. The error message must contain 'No GPU nodes found' when no GPU nodes exist at all, and must contain the names of available GPU types when GPU nodes exist but the requested type is not among them.

*   The resolve_gres_gpu_type function must filter nodes by partition when specified, stripping a trailing '*' from partition names before comparison. It must only consider nodes with GPU count >= the requested count.

*   When multiple GRES types match the requested GPU name, resolve_gres_gpu_type must resolve ambiguity in this order: (1) exact case-insensitive match wins, (2) the type available on more nodes wins, (3) alphabetical order as a tiebreaker.

*   The canonicalize_raw_gpu_name function in sky/provision/slurm/utils.py must accept a raw GRES string and return the canonical GPU name. It must use CANONICAL_GPU_NAMES and _accelerator_name_matches_slurm for matching. RTX A-series GPUs like 'nvidia_rtx_a6000' must return 'A6000' and 'nvidia_rtx_a5000' must return 'A5000'. RTX non-A-series like 'nvidia_rtx_6000_ada' and 'nvidia_rtx_3090' must fall back to the uppercased original string (e.g., 'NVIDIA_RTX_6000_ADA', 'NVIDIA_RTX_3090'). Unknown GPU names must also fall back to the uppercased original.

*   The check_instance_fits function in sky/provision/slurm/utils.py must accept (cluster: str, instance_type: str, partition: str = None) and return a (bool, str) tuple. When the GPU type is not found, it must return (False, reason) where reason includes available GPU types. When GPU nodes exist but none match the requested type, the reason must contain 'No GPU nodes matching'. L4 must not fit nodes with L40 GPUs.

*   The function formerly named get_gres_gpu_type in sky/provision/slurm/utils.py must be renamed or replaced by resolve_gres_gpu_type with the new signature (cluster: str, requested: str, count: int = 1, partition: str = None) -> str. Any code that previously mocked get_gres_gpu_type must instead mock resolve_gres_gpu_type.


*   Interface details: Type: Constant
Name: CANONICAL_GPU_NAMES
Location: sky/utils/gpu_names.py
Description: List of canonical GPU names in priority order (longer/more specific names before shorter ones, e.g. 'L40S' before 'L40' before 'L4'). Moved from sky/provision/kubernetes/constants.py. Must include at minimum: B200, H100, H200, L40S, L40, L4, A100, A10, V100, GH200, A6000, A5000.

Type: Function
Name: _normalize_gpu_name
Location: sky/provision/slurm/utils.py
Signature: _normalize_gpu_name(raw: str) -> str
Description: Normalizes a raw GPU name string. Strips leading vendor prefixes ('nvidia_' and 'tesla_' prefix, case-insensitive). RTX is NOT a vendor prefix and must be preserved. The 'amd_' prefix is also stripped. Converts all underscores to dashes and lowercases the result. Empty string returns empty string. Mixed separators (e.g. 'nvidia_a100-sxm4-80gb') are handled: strip vendor prefix, keep remaining dashes intact.

Type: Function
Name: _accelerator_name_matches_slurm
Location: sky/provision/slurm/utils.py
Signature: _accelerator_name_matches_slurm(requested: str, slurm_gres: str) -> bool
Description: Returns True if the requested GPU name matches the Slurm GRES string, False otherwise. Uses _normalize_gpu_name on both sides. Performs subsequence token matching (tokens of requested must appear as a subsequence of tokens of slurm_gres after normalization). Must be case-insensitive. Key rules:
- 'H100' matches 'NVIDIA_H100_80GB_S' (subsequence: ['h100'] is subsequence of ['h100','80gb','s'])
- 'H100-80GB' does NOT match 'H100' alone (more specific does not match less specific)
- 'L4' must NOT match 'L40' or 'L40S'
- 'A100' matches 'A100-SXM-80GB' but 'A100-80GB' does NOT match 'nvidia_a100_pcie_40gb'
- RTX: 'RTX-A6000' (dash form) matches 'nvidia_rtx_a6000'; 'RTXA6000' (dashless) does NOT match

Type: Function
Name: resolve_gres_gpu_type
Location: sky/provision/slurm/utils.py
Signature: resolve_gres_gpu_type(cluster: str, requested: str, count: int, partition: str = None) -> str
Description: Resolves a requested GPU type name to the actual GRES GPU type string available on the cluster. Internally uses _get_slurm_nodes_info and get_cluster_default_partition. Filters nodes by partition (strips trailing '*' from partition names before comparison). Filters nodes by GPU count >= count. Returns the raw GRES type string. When multiple GRES types match the requested name, uses ambiguity resolution: (1) exact case-insensitive match wins, (2) type available on more nodes wins, (3) alphabetical tie-break. Raises sky.exceptions.ResourcesUnavailableError:
- When no GPU nodes exist at all: error message contains 'No GPU nodes found'
- When GPU type not available: error message includes available GPU types (e.g., 'A100')
- When GPU exists but count not satisfied or wrong partition: raises ResourcesUnavailableError

Type: Function
Name: canonicalize_raw_gpu_name
Location: sky/provision/slurm/utils.py
Signature: canonicalize_raw_gpu_name(raw: str) -> str
Description: Converts a raw GRES GPU name to the canonical GPU name used throughout SkyPilot. Uses CANONICAL_GPU_NAMES list and _accelerator_name_matches_slurm for matching. RTX A-series GPUs (A6000, A5000) are matched via subsequence. RTX non-A-series (e.g., 6000-Ada, 3090) fall back to uppercased original string. Unknown/unmatched GPU names fall back to the uppercased original. Examples:
- 'nvidia_h100_80gb_hbm3' -> 'H100-80GB'
- 'nvidia_l40s' -> 'L40S'
- 'NVIDIA_A100_SXM4_80GB' -> 'A100-80GB'
- 'H100' -> 'H100'
- 'unknown_custom_gpu' -> 'UNKNOWN_CUSTOM_GPU'
- 'nvidia_rtx_a6000' -> 'A6000'
- 'nvidia_rtx_6000_ada' -> 'NVIDIA_RTX_6000_ADA'
- 'nvidia_rtx_3090' -> 'NVIDIA_RTX_3090'

Type: Function
Name: check_instance_fits
Location: sky/provision/slurm/utils.py
Signature: check_instance_fits(cluster: str, instance_type: str, partition: str = None) -> tuple
Description: Checks whether a given instance type (e.g. '4CPU--16GB--H100:8') can be satisfied by available nodes on the cluster. Returns (True, None) if the instance fits, (False, reason_str) if not. The reason_str when GPU type is not available includes the available GPU types. The reason contains 'No GPU nodes matching' when GPU nodes exist but none match the requested GPU type. L4 does NOT match L40 nodes. Uses resolve_gres_gpu_type internally and returns (False, str(exception)) when ResourcesUnavailableError is raised.

Type: Function
Name: _get_slurm_nodes_info
Location: sky/provision/slurm/utils.py
Signature: _get_slurm_nodes_info(cluster: str) -> list
Description: Internal function used by resolve_gres_gpu_type to retrieve node information from a Slurm cluster. Must exist at this exact name in sky/provision/slurm/utils.py. Tests mock this function as 'sky.provision.slurm.utils._get_slurm_nodes_info'.

Type: Function
Name: get_cluster_default_partition
Location: sky/provision/slurm/utils.py
Signature: get_cluster_default_partition(cluster: str) -> str
Description: Module-level function that returns the default partition name for a Slurm cluster. Must exist at this exact name in sky/provision/slurm/utils.py. Tests mock this function as 'sky.provision.slurm.utils.get_cluster_default_partition'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.