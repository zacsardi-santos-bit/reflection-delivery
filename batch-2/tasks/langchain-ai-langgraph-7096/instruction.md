I'm working on the LangGraph CLI and need to add support for a distributed deployment mode.

*   The default_base_image function must accept an optional engine_runtime_mode parameter (default: 'combined_queue_worker'). When called with no mode or with 'combined_queue_worker' on a Python config, it must return 'langchain/langgraph-api'. When called with 'distributed' on a Python config, it must return 'langchain/langgraph-executor'. For Node.js configs, it must return 'langchain/langgraphjs-api' regardless of mode. If config['base_image'] is explicitly set, it must return that value regardless of engine_runtime_mode.

*   The config_to_compose function must accept an optional engine_runtime_mode parameter (default: 'combined_queue_worker'). When engine_runtime_mode='distributed', the output must contain two FROM lines: one for 'langchain/langgraph-api' (with the Python version tag) for the API service, and one for 'langchain/langgraph-executor' (with the Python version tag) for the executor service.

*   When config_to_compose is called with engine_runtime_mode='distributed', the output must include a 'langgraph-orchestrator:' service with an environment variable 'EXECUTOR_TARGET: langgraph-executor:8188'.

*   When config_to_compose is called with engine_runtime_mode='distributed', the output must include a 'langgraph-executor:' service with entrypoint ['sh', '/storage/executor_entrypoint.sh'] and environment variables: EXECUTOR_GRPC_PORT, ENGINE_GRPC_ADDRESS, LSD_GRPC_SERVER_ADDRESS, LANGGRAPH_HTTP (set to empty string), and REDIS_URI set to 'redis://langgraph-redis:6379'.

*   When config_to_compose is called with engine_runtime_mode='distributed' and the config specifies an env file, the env_file directive must appear exactly 3 times in the output (once each for the API, orchestrator, and executor services).

*   When config_to_compose is called with engine_runtime_mode='distributed', both the API and executor Dockerfiles must contain a valid LANGSERVE_GRAPHS= line (2 total LANGSERVE_GRAPHS= occurrences in the output). A deep copy of the config must be used before generating the executor Dockerfile so that path mutations from the API Dockerfile generation do not corrupt the executor Dockerfile.

*   When config_to_compose is called with engine_runtime_mode='combined_queue_worker' or without specifying engine_runtime_mode, the output must NOT contain 'langgraph-orchestrator:' or 'langgraph-executor:' service definitions.

*   The compose function in docker.py must accept an optional engine_runtime_mode parameter (default: 'combined_queue_worker'). When engine_runtime_mode='distributed', it must add N_JOBS_PER_WORKER: "0" to the langgraph-api service environment block. When engine_runtime_mode='combined_queue_worker', N_JOBS_PER_WORKER must not appear in the output.

*   The prepare_args_and_stdin function must accept an optional engine_runtime_mode parameter (default: 'combined_queue_worker'). When engine_runtime_mode='distributed', the returned stdin must contain: 'FROM langchain/langgraph-api:', 'N_JOBS_PER_WORKER: "0"', 'langgraph-orchestrator:', 'langgraph-executor:', 'FROM langchain/langgraph-executor:', and 'executor_entrypoint.sh'.

*   The CLI 'dockerfile' command must accept a new --engine-runtime-mode option with allowed values 'combined_queue_worker' and 'distributed' (default: 'combined_queue_worker'). When --engine-runtime-mode=distributed and no --base-image is specified, the generated Dockerfile must use 'langchain/langgraph-executor' as the base image (e.g. 'FROM langchain/langgraph-executor:3.11'). When --engine-runtime-mode=combined_queue_worker, the Dockerfile must use 'langchain/langgraph-api' (e.g. 'FROM langchain/langgraph-api:3.11'). When both --engine-runtime-mode=distributed and --base-image=my-custom-executor:latest are given, the Dockerfile must use 'FROM my-custom-executor:latest'.

*   When the CLI 'dockerfile' command with --engine-runtime-mode=distributed succeeds, the exit code must be 0 and the output must include '✅ Created: Dockerfile'.


*   Interface details: Type: Function
Name: default_base_image
Location: libs/cli/langgraph_cli/config.py
Signature: default_base_image(config: Config, engine_runtime_mode: str = "combined_queue_worker") -> str
Description: Returns the default Docker base image name for a given config and runtime mode. If config["base_image"] is explicitly set, returns it regardless of mode. For Node.js configs (node_version set, no python_version), returns "langchain/langgraphjs-api". For Python configs with engine_runtime_mode="distributed", returns "langchain/langgraph-executor". Otherwise returns "langchain/langgraph-api".

Type: Function
Name: config_to_compose
Location: libs/cli/langgraph_cli/config.py
Signature: config_to_compose(config_path: pathlib.Path, config: Config, base_image: str, ..., engine_runtime_mode: str = "combined_queue_worker") -> str
Description: Generates a Docker Compose YAML string. When engine_runtime_mode="distributed", the output includes a second Dockerfile for an executor service (using "langchain/langgraph-executor" as base image), plus langgraph-orchestrator and langgraph-executor service definitions with specific environment variables. Uses a deep copy of config before generating the executor Dockerfile so that path mutations from the API Dockerfile generation do not affect the executor. When engine_runtime_mode="combined_queue_worker" or not specified, no orchestrator or executor services are added.

Type: Function
Name: compose
Location: libs/cli/langgraph_cli/docker.py
Signature: compose(capabilities, port: int, ..., engine_runtime_mode: str = "combined_queue_worker") -> str
Description: Generates a Docker Compose YAML string for the docker run setup. When engine_runtime_mode="distributed", adds N_JOBS_PER_WORKER: "0" to the langgraph-api service environment block. When engine_runtime_mode="combined_queue_worker" or not specified, N_JOBS_PER_WORKER is not included.

Type: Function
Name: prepare_args_and_stdin
Location: libs/cli/langgraph_cli/cli.py
Signature: prepare_args_and_stdin(capabilities, config_path: pathlib.Path, config: Config, docker_compose, port: int, watch: bool, ..., engine_runtime_mode: str = "combined_queue_worker", ...) -> tuple[list, str]
Description: Prepares Docker Compose arguments and the STDIN content (compose YAML) for running the LangGraph server. When engine_runtime_mode="distributed", the returned stdin contains: "FROM langchain/langgraph-api:", 'N_JOBS_PER_WORKER: "0"', "langgraph-orchestrator:", "langgraph-executor:", "FROM langchain/langgraph-executor:", and "executor_entrypoint.sh".

Type: CLI Command
Name: dockerfile
Location: libs/cli/langgraph_cli/cli.py
Description: The CLI "dockerfile" command accepts a new --engine-runtime-mode option. Valid values are "combined_queue_worker" (default) and "distributed". When --engine-runtime-mode=distributed and no --base-image is given, the generated Dockerfile uses "langchain/langgraph-executor" as the base image (with the Python version tag appended, e.g. "langchain/langgraph-executor:3.11"). When --engine-runtime-mode=combined_queue_worker, the Dockerfile uses "langchain/langgraph-api" as the base image. When both --engine-runtime-mode=distributed and --base-image are given, the explicit --base-image overrides the executor default.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.