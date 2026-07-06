I'm running into an issue where reading pod logs asynchronously from Kubernetes throws an encoding error whenever the container output contains bytes that aren't valid UTF-8.

*   The read_logs method of AsyncKubernetesHook must pass _preload_content=False when calling the pod log API, so the response is a raw HTTP response object rather than a pre-decoded string.

*   After calling the pod log API, the implementation must call .read() on the raw response object to obtain the log content as bytes.

*   The raw bytes must be decoded to a UTF-8 string using error replacement mode, so that any byte sequences that are not valid UTF-8 are replaced with the Unicode replacement character U+FFFD (\ufffd) instead of raising a UnicodeDecodeError.

*   The decoded string must be split into lines and returned as a list of strings.

*   When pod log bytes contain a mix of valid and invalid UTF-8 sequences, the returned list must include one entry per line, with invalid bytes on any line replaced by U+FFFD — the number of returned lines must match the number of newline-delimited segments in the original byte content.

*   The GKE async Kubernetes hook's read_logs method must also pass _preload_content=False to the pod log API, and handle the resulting raw response the same way.


*   Interface details: Type: Method
Name: read_logs
Location: providers/cncf/kubernetes/src/airflow/providers/cncf/kubernetes/hooks/kubernetes.py
Signature: async read_logs(self, name: str, namespace: str, container_name: str = "", since_seconds: int | None = None) -> list[str]
Description: Reads pod logs asynchronously. Must call the underlying pod log API with _preload_content=False to obtain a raw response object, then call .read() on that response to get bytes, decode those bytes using UTF-8 with errors="replace" (so non-UTF-8 bytes become U+FFFD rather than raising UnicodeDecodeError), and return the decoded text split into lines as a list of strings. This method belongs to the AsyncKubernetesHook class.

---

Type: Method
Name: read_logs
Location: providers/google/src/airflow/providers/google/cloud/hooks/kubernetes_engine.py
Signature: async read_logs(self, name: str, namespace: str, container_name: str = "", since_seconds: int | None = None) -> list[str]
Description: Reads pod logs asynchronously for GKE clusters. Must call the underlying pod log API with _preload_content=False to obtain a raw response object, then call .read() on that response to get bytes, decode those bytes using UTF-8 with errors="replace" (so non-UTF-8 bytes become U+FFFD rather than raising UnicodeDecodeError), and return the decoded text split into lines as a list of strings. This method belongs to the GKEKubernetesAsyncHook class.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.