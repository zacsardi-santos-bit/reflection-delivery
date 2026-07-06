I'm working on DataHub's metadata ingestion framework and need to add support for retrieving secrets stored in cloud-hosted secret management services.

*   AwsSecretsManagerStore must be instantiated via a class method called create() that accepts a configuration dictionary with keys: region (string), prefix (string), and cache_ttl (integer).

*   AwsSecretsManagerStore.get_id() must return the string 'aws-sm'.

*   AwsSecretsManagerStore.get_secret_values(keys) must accept a list of logical secret names, prepend the configured prefix to each name, look them up in AWS Secrets Manager, and return a dictionary mapping each original (unprefixed) key to its string value or to None if the secret does not exist.

*   AwsSecretsManagerStore.get_secret_values([]) must return an empty dictionary when given an empty list.

*   AwsSecretsManagerStore.get_secret_value(key) must accept a single logical secret name and return its string value, or None if the secret does not exist.

*   GcpSecretManagerStore must be instantiated via a class method called create() that accepts a configuration dictionary with keys: project_id (string), prefix (string), and cache_ttl (integer).

*   GcpSecretManagerStore.get_id() must return the string 'gcp-sm'.

*   GcpSecretManagerStore.get_secret_values(keys) must accept a list of logical secret names and look each one up in GCP Secret Manager using the resource path format 'projects/{project_id}/secrets/{prefix}{key}/versions/latest'. The call must pass this path as a keyword argument named 'request' (as a dict with a 'name' key) and also pass a 'retry' keyword argument to the underlying GCP client method. The response payload bytes must be decoded as UTF-8. Returns a dictionary mapping each original key to its decoded string value or to None if an exception is raised during retrieval.

*   GcpSecretManagerStore.get_secret_values([]) must return an empty dictionary when given an empty list.

*   GcpSecretManagerStore.get_secret_value(key) must accept a single logical secret name and return its string value, or None if the secret does not exist or an exception is raised.

*   Both stores must correctly handle a mix of existing and missing secrets in a single get_secret_values() call, returning the values for found secrets and None for each missing secret without raising an exception.


*   Interface details: Type: Class
Name: AwsSecretsManagerStore
Location: metadata-ingestion/src/datahub/secret/aws_secret_store.py
Description: Secret store implementation that retrieves secrets from AWS Secrets Manager. Accepts a configurable prefix that is prepended to logical key names before lookups.
Signature:
  create(config: dict) -> AwsSecretsManagerStore   [classmethod or staticmethod]
    config keys: region (str), prefix (str), cache_ttl (int)
  get_id() -> str
    Returns the string "aws-sm"
  get_secret_values(keys: List[str]) -> Dict[str, Optional[str]]
    Prepends prefix to each key, looks up in AWS Secrets Manager.
    Returns dict mapping each original (unprefixed) key to its value string, or None if the secret is not found.
    Returns {} when keys is empty.
  get_secret_value(key: str) -> Optional[str]
    Single-key convenience wrapper; returns value string or None.

Type: Class
Name: GcpSecretManagerStore
Location: metadata-ingestion/src/datahub/secret/gcp_secret_store.py
Description: Secret store implementation that retrieves secrets from Google Cloud Secret Manager. Constructs resource paths using the configured project_id and prefix.
Signature:
  create(config: dict) -> GcpSecretManagerStore   [classmethod or staticmethod]
    config keys: project_id (str), prefix (str), cache_ttl (int)
  get_id() -> str
    Returns the string "gcp-sm"
  get_secret_values(keys: List[str]) -> Dict[str, Optional[str]]
    For each key, constructs path "projects/{project_id}/secrets/{prefix}{key}/versions/latest".
    Calls google.cloud.secretmanager.SecretManagerServiceClient().access_secret_version
      with keyword argument request={"name": <path>} and a retry keyword argument.
    Decodes response.payload.data as UTF-8 to obtain the secret string value.
    Returns None for any key where access raises an exception.
    Returns {} when keys is empty.
  get_secret_value(key: str) -> Optional[str]
    Single-key convenience wrapper; returns value string or None.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.