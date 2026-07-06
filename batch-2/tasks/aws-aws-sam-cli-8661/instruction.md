I'd like to add support for an optional build mode in SAM CLI that uses the container runtime's native CLI tooling when building Lambda container image functions, instead of always going through the SDK.

*   A new module at samcli/local/docker/image_build_client.py must define the ImageBuildClient abstract base class and two concrete implementations: SDKBuildClient and CLIBuildClient.

*   SDKBuildClient must accept a container client at construction and implement build_image by delegating to container_client.images.build, always including rm=True when rm is not explicitly overridden. It must return the build log iterator (second element) from the underlying call. Its is_available static method must return (True, None) for any engine type.

*   CLIBuildClient must accept engine_type at construction and store it as both engine_type and cli_command attributes. For 'docker', build_image must produce a subprocess command starting with ['docker', 'buildx', 'build'] and include '--provenance=false', '--sbom=false', '--load' flags. For 'finch', the command starts with ['finch', 'build']. In both cases, the dockerfile path is joined to the context path when not absolute. Optional arguments (platform, buildargs, target, rm) are appended when provided. Each line of subprocess stdout must be yielded as a dict with key 'stream'. On non-zero exit code, docker.errors.BuildError must be raised with message 'Build failed with exit code {code}' and a build_log attribute containing all collected log entries.

*   CLIBuildClient.is_available for engine_type 'docker' must: return (False, 'Docker CLI not found') if shutil.which('docker') returns None; run subprocess.run(['docker', 'buildx', 'version'], capture_output=True, check=False) and return (False, 'docker buildx plugin not available') if returncode is non-zero; otherwise return (True, None).

*   CLIBuildClient.is_available for engine_type 'finch' must: return (False, 'Finch CLI not found') if shutil.which('finch') returns None; run subprocess.run(['finch', 'version'], capture_output=True, check=False) and return (True, None) on success.

*   CLIBuildClient.is_available for any unknown engine type must return (False, 'Unknown engine type: {engine_type}').

*   A new exception class BuildkitNotAvailableException must be added to samcli/local/docker/exceptions.py as a subclass of UserException. Its string representation must include the error message passed at construction.

*   ApplicationBuilder.__init__ must rename its docker_client parameter to container_client and add a use_buildkit: Optional[bool] = False parameter. It must initialize a _image_build_client attribute to None.

*   During image builds, ApplicationBuilder must lazily initialize _image_build_client: when use_buildkit is False, initialize with SDKBuildClient(container_client); when use_buildkit is True, retrieve the engine type via container_client.get_runtime_type(), call CLIBuildClient.is_available(engine_type), raise BuildkitNotAvailableException with the error message if unavailable, or initialize with CLIBuildClient(engine_type=engine_type) if available.

*   BuildContext.__init__ must accept use_buildkit: Optional[bool] = False and pass it to ApplicationBuilder.

*   do_cli in samcli/commands/build/command.py must accept use_buildkit: Optional[bool] as a parameter and forward it to BuildContext.


*   Interface details: Type: Class
Name: ImageBuildClient
Location: samcli/local/docker/image_build_client.py
Description: Abstract base class for container image build clients. Provides the interface that both SDK-based and CLI-based implementations must satisfy.
Signature:
  build_image(path: str, dockerfile: str, tag: str, buildargs: Optional[Dict[str, str]] = None, platform: Optional[str] = None, target: Optional[str] = None, rm: bool = True) -> Generator[Dict[str, Any], None, None]
  is_available(engine_type: str) -> Tuple[bool, Optional[str]]  [staticmethod, abstractmethod]

Type: Class
Name: SDKBuildClient
Location: samcli/local/docker/image_build_client.py
Description: ImageBuildClient implementation that uses the docker-py SDK to build container images. Constructed with a container client object.
Signature:
  __init__(container_client: ContainerClient) -> None
  build_image(path: str, dockerfile: str, tag: str, buildargs: Optional[Dict[str, str]] = None, platform: Optional[str] = None, target: Optional[str] = None, rm: bool = True) -> Generator[Dict[str, Any], None, None]
  is_available(engine_type: str) -> Tuple[bool, Optional[str]]  [staticmethod]

SDKBuildClient behavior:
- build_image calls container_client.images.build(**kwargs) forwarding all provided arguments. The rm=True default is always passed even when not explicitly provided by the caller.
- build_image returns the second element (build logs) from container_client.images.build return value.
- is_available always returns (True, None) for any engine_type.

Type: Class
Name: CLIBuildClient
Location: samcli/local/docker/image_build_client.py
Description: ImageBuildClient implementation that uses the container runtime CLI (docker buildx or finch) to build container images.
Signature:
  __init__(engine_type: str) -> None
  build_image(path: str, dockerfile: str, tag: str, buildargs: Optional[Dict[str, str]] = None, platform: Optional[str] = None, target: Optional[str] = None, rm: bool = True) -> Generator[Dict[str, Any], None, None]
  is_available(engine_type: str) -> Tuple[bool, Optional[str]]  [staticmethod]

CLIBuildClient attributes:
- engine_type: str — stores the provided engine type (e.g. "docker" or "finch")
- cli_command: str — stores the CLI command name; equals engine_type

CLIBuildClient.build_image behavior:
- If dockerfile is not an absolute path, it is joined with path: os.path.join(path, dockerfile)
- For engine_type "docker", the command is: [docker, buildx, build, -f, <dockerfile>, -t, <tag>, --provenance=false, --sbom=false, --load, [--platform <platform>], [--build-arg k=v ...], [--target <target>], [--rm], <path>]
- For engine_type "finch", the command is: [finch, build, -f, <dockerfile>, -t, <tag>, [--platform <platform>], [--build-arg k=v ...], [--target <target>], [--rm], <path>]
- --rm flag is included when rm=True
- Output lines from subprocess stdout are each yielded as {"stream": <line>}
- If subprocess returns non-zero exit code, raises docker.errors.BuildError with message "Build failed with exit code {returncode}" and build_log set to the list of all {"stream": line} log entries collected during the run
- Uses subprocess.Popen to run the command

CLIBuildClient.is_available behavior:
- For engine_type "docker":
  - If shutil.which("docker") is None: return (False, "Docker CLI not found")
  - Run subprocess.run(["docker", "buildx", "version"], capture_output=True, check=False)
  - If returncode != 0: return (False, "docker buildx plugin not available")
  - Otherwise: return (True, None)
- For engine_type "finch":
  - If shutil.which("finch") is None: return (False, "Finch CLI not found")
  - Run subprocess.run(["finch", "version"], capture_output=True, check=False)
  - If returncode == 0: return (True, None)
- For any other engine_type: return (False, "Unknown engine type: {engine_type}")

Type: Exception
Name: BuildkitNotAvailableException
Location: samcli/local/docker/exceptions.py
Description: Raised when buildkit CLI mode is requested but the required CLI tooling is unavailable. Must be a subclass of UserException. The string representation of the exception must include the error message passed at construction.

Type: Class
Name: ApplicationBuilder
Location: samcli/lib/build/app_builder.py
Description: Existing class; requires parameter and attribute changes.
Signature (changed/added parameters):
  __init__(..., container_client: Optional[ContainerClient] = None, ..., use_buildkit: Optional[bool] = False) -> None
  Note: The parameter formerly named docker_client is renamed to container_client.

ApplicationBuilder attribute changes:
- _image_build_client: Optional[ImageBuildClient] — initialized to None; lazily set during _build_lambda_image
- When use_buildkit=False: _image_build_client is set to SDKBuildClient(container_client)
- When use_buildkit=True: engine_type is retrieved via container_client.get_runtime_type(); CLIBuildClient.is_available(engine_type) is called; if not available, BuildkitNotAvailableException is raised with the error message; if available, _image_build_client is set to CLIBuildClient(engine_type=engine_type)

Type: Function
Name: do_cli
Location: samcli/commands/build/command.py
Description: Existing function; must accept a new use_buildkit parameter and pass it to BuildContext.
Signature: do_cli(..., mount_symlinks: Optional[bool], use_buildkit: Optional[bool]) -> None

Type: Class
Name: BuildContext
Location: samcli/commands/build/build_context.py
Description: Existing class; must accept use_buildkit in __init__ and pass it to ApplicationBuilder.
Signature: __init__(..., mount_symlinks: Optional[bool] = False, use_buildkit: Optional[bool] = False) -> None


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.