I'm working with Salt's SSH PKI module and I've run into two gaps I'd like to fix.

*   When create_private_key is called with a path argument, it must create a companion public key file at the same path with a '.pub' suffix appended. The content of this public key file must equal the public key derivable from the generated private key (i.e., calling the public-key extraction function on the private key path must return the same string stored in the .pub file).

*   The private key file created by create_private_key must still contain valid OpenSSH private key data (starting with '-----BEGIN OPENSSH PRIVATE KEY-----') and must have file permissions set to 0o600 (octal).

*   The merge_signing_policy function must accept 'not_before' and 'not_after' datetime strings in the kwargs argument. These strings must use the TIME_FMT format defined in salt.utils.x509 ('%Y-%m-%d %H:%M:%S').

*   When merge_signing_policy is called with no TTL or max_ttl in the policy and both 'not_before' and 'not_after' are provided in kwargs, those values must be passed through unchanged in the returned result.

*   When merge_signing_policy is called with a TTL or max_ttl policy constraint and both 'not_before' and 'not_after' are provided in kwargs, the resulting 'not_after' must be capped so that the validity window does not exceed the effective TTL (min of ttl and max_ttl). The resulting 'ttl' must be the actual number of seconds between the final not_before and not_after.

*   When merge_signing_policy is called with a max_ttl constraint and only 'not_before' is provided, the function must set 'not_after' to not_before + max_ttl seconds and set 'ttl' to that number of seconds.

*   When merge_signing_policy is called with a max_ttl constraint and only 'not_after' is provided, the function must use the current time as 'not_before' and cap 'not_after' to min(provided_not_after, now + max_ttl). The 'ttl' must reflect the effective duration in seconds.

*   When merge_signing_policy results in an effective TTL (from policy or computed dates), the function must include both 'not_before' and 'not_after' as TIME_FMT-formatted strings in the returned kwargs, in addition to the numeric 'ttl' value. When no TTL is set and no dates are provided, these fields must not be present.

*   The merge_signing_policy function in salt/utils/sshpki.py must access the current time via datetime.now() on the datetime class imported at the module level (so that tests can patch 'salt.utils.sshpki.datetime' to control the current time during testing).


*   Interface details: Type: Function
Name: create_private_key
Location: salt/modules/ssh_pki.py
Signature: create_private_key(algo="ed25519", keysize=None, passphrase=None, path=None, pubkey_suffix=".pub", overwrite=False, raw=False) -> str | dict | bytes
Description: Creates a new SSH private key. When path is provided, writes the private key to that file and also writes the public key to path + pubkey_suffix (default ".pub"). The public key file content must equal the result of calling get_public_key on the generated private key. The private key file must have permissions 0o600. When path is None and raw is False, returns a dict with "private_key" and "public_key" fields.

Type: Function
Name: merge_signing_policy
Location: salt/utils/sshpki.py
Signature: merge_signing_policy(policy: dict, kwargs: dict) -> dict
Description: Merges a signing policy dict into a kwargs dict, enforcing TTL and principal constraints. Extended to handle "not_before" and "not_after" datetime string parameters in kwargs (formatted as TIME_FMT = "%Y-%m-%d %H:%M:%S" from salt.utils.x509). When a TTL or max_ttl constraint is active, enforces validity window limits and populates "not_before", "not_after", and "ttl" in the returned kwargs. When explicit dates are provided with no TTL constraint, passes them through unchanged. Modifies and returns kwargs.

Type: Constant
Name: TIME_FMT
Location: salt/utils/x509.py
Signature: TIME_FMT = "%Y-%m-%d %H:%M:%S"
Description: The datetime format string used for "not_before" and "not_after" parameters in merge_signing_policy and related functions.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.