I'm working on adding support for running NVIDIA inference model servers as sidecar containers in Flyte task pods. I need to implement a configuration class for the model server and a separate credentials class for the NGC authentication details.

The credentials class should require the NGC API key name, the image pull secret name, and a secrets prefix — all three are mandatory, and attempting to create the credentials without any of them should fail with a clear error.

The model server configuration class should accept the credentials along with optional parameters for the model image, memory allocation, port, and environment variables. It should automatically set up the pod template with the correct image pull secret, inject the NGC API key as an environment variable using the pattern of combining the secrets prefix with the uppercased key name, and apply the given resource and port settings. The class should expose sensible defaults: a base URL pointing to localhost on port 8000, one CPU, one GPU, 20Gi of memory, 16Gi of shared memory, and a standard health check endpoint path.

Additionally, I want to support downloading LoRA fine-tuned adapters from Hugging Face before the model server starts. When a list of Hugging Face repository IDs is provided, the system should validate that a memory allocation for the download step is also specified (raising a descriptive error if not), and that the required source path environment variable is set (raising a descriptive error if not). When both are present, it should configure a dedicated init container that downloads all specified adapters.

Both the model server configuration class and the credentials class should be importable from the inference plugin's top-level namespace.
