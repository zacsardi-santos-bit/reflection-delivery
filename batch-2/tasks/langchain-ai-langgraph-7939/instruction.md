I'm working on the LangGraph CLI and need to add a feature for tracking specific third-party packages across deployments.

*   A new module `langgraph_cli.dependency_tracking` must be created at `libs/cli/langgraph_cli/dependency_tracking.py` and must export two names: `TRACKED_PACKAGES` and `find_tracked_packages`.

*   TRACKED_PACKAGES must be an iterable of package name strings containing at least 'google-adk'. Every package name in TRACKED_PACKAGES must be individually detectable by find_tracked_packages when present in a dependency file.

*   find_tracked_packages(config, config_json) must return a list of strings formatted as 'package_name:version_specifier' for each tracked package found across the declared dependency paths.

*   When a uv.lock file is present in a dependency directory and contains a tracked package, find_tracked_packages must use the resolved (exact) version from the lock file, returning 'package:X.Y.Z'.

*   When no uv.lock is present but a pyproject.toml is found with the tracked package in its dependencies list, find_tracked_packages must return the version specifier as declared, e.g. 'google-adk:>=0.5,<2'.

*   When a requirements.txt is found and the tracked package is listed with a version constraint, find_tracked_packages must return the constraint, e.g. 'google-adk:==1.0.0'. When the package appears without any version specifier, find_tracked_packages must return 'package:unknown'.

*   When a tracked package appears only as an extras dependency inside brackets (e.g., 'other-pkg[google-adk]>=0.0.1'), find_tracked_packages must return 'google-adk:unknown'.

*   find_tracked_packages must silently ignore dependency entries in config_json['dependencies'] that are not strings (e.g., integers or None).

*   find_tracked_packages must skip any dependency path that resolves to a location outside the project root (the directory containing the config file). No packages from such paths may be reported.

*   find_tracked_packages must handle dependency files larger than 5 MB by reading only up to a size cap (approximately 5 MB). Content beyond the cap is silently discarded; no exception is raised. If the relevant tracked-package marker falls beyond the cap, it is not detected.

*   find_tracked_packages must scan multiple dependency paths in the order they appear in config_json['dependencies'] and return results from whichever path contains a match first.

*   find_tracked_packages must return an empty list when none of the scanned dependency files contain any package from TRACKED_PACKAGES.

*   The update_deployment method on HostBackendClient must accept an optional tracked_packages keyword argument (a list of strings, defaulting to None). When provided, tracked_packages must appear as a top-level key in the JSON request body and must NOT appear inside the 'source_revision_config' sub-object. When omitted or None, the 'tracked_packages' key must be entirely absent from the request body.

*   The update_deployment_internal_source method on HostBackendClient must accept an optional tracked_packages keyword argument (a list of strings, defaulting to None). When provided, tracked_packages must appear as a top-level key in the JSON request body and must NOT appear inside the 'source_revision_config' sub-object. The 'source_tarball_path' value must continue to be placed inside 'source_revision_config'. When omitted or None, the 'tracked_packages' key must be entirely absent from the request body.


*   Interface details: Type: Constant
Name: TRACKED_PACKAGES
Location: libs/cli/langgraph_cli/dependency_tracking.py
Description: An iterable (e.g., list or set) of package name strings that the dependency tracker looks for. Must contain at least "google-adk". The parametrized test iterates over every entry in TRACKED_PACKAGES and verifies that each can be detected.

Type: Function
Name: find_tracked_packages
Location: libs/cli/langgraph_cli/dependency_tracking.py
Signature: find_tracked_packages(config: pathlib.Path, config_json: dict) -> list[str]
Description: Scans the dependency directories declared in config_json["dependencies"] (resolved relative to the directory containing the config file) and returns a list of strings in the format "package_name:version_specifier" for every package found that is in TRACKED_PACKAGES. The config parameter is the path to the langgraph.json file and its parent directory is treated as the project root.

Type: Class Method (existing class — signature change only)
Name: update_deployment
Location: libs/cli/langgraph_cli/host_backend.py
Signature: update_deployment(self, deployment_id: str, image: str, ..., tracked_packages: list[str] | None = None, ...) -> ...
Description: Updated to accept an optional tracked_packages keyword argument (default None). When tracked_packages is provided (not None), it must be included as a top-level "tracked_packages" key in the JSON request body. The "tracked_packages" key must NOT appear inside the "source_revision_config" sub-object. When tracked_packages is None, the "tracked_packages" key must be completely absent from the request body.

Type: Class Method (existing class — signature change only)
Name: update_deployment_internal_source
Location: libs/cli/langgraph_cli/host_backend.py
Signature: update_deployment_internal_source(self, deployment_id: str, source_tarball_path: str, config_path: str, ..., tracked_packages: list[str] | None = None, ...) -> ...
Description: Updated to accept an optional tracked_packages keyword argument (default None). When tracked_packages is provided (not None), it must be included as a top-level "tracked_packages" key in the JSON request body. The "tracked_packages" key must NOT appear inside the "source_revision_config" sub-object. The source_tarball_path value continues to be placed inside "source_revision_config". When tracked_packages is None, the "tracked_packages" key must be completely absent from the request body.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.