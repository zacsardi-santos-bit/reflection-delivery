I'd like to add automated content scanning to the package upload flow.

*   The warehouse/utils/scanner.py module must define a frozen dataclass YaraMatch with three string fields: rule (the matched rule identifier), member (the archive member path that triggered the match), and message (the user-facing explanation from rule metadata).

*   compile_rules(rules_dir=None) must compile all *.yar files from the given directory (defaulting to a built-in scanner_rules directory). It must return None if the directory contains no .yar files, if any rule file fails to compile, or on any other error (fail-open). Non-.yar files (e.g., .txt) must be ignored. Every compiled rule must include a 'message' metadata field.

*   _get_rule_message(rule) must extract and return the string value of the 'message' metadata field from a matched YARA rule object.

*   check_members(members, rules=None, *, archive_name='') must iterate over (path_str, size_int, bytes) tuples. archive_name must be a keyword-only argument. It must skip any member whose size exceeds the module-level _SCAN_MAX_FILE_SIZE constant. It must return a YaraMatch for the first member that matches a YARA rule, or None if no member matches.

*   check_members must fall back to the module-level _rules variable when no explicit rules argument is provided, and must return None when both the argument and module-level variable are None.

*   check_members must return None when a yara_x.ScanError occurs during scanning (fail-open).

*   check_members must avoid false positives from cross-boundary matches: if a bulk scan reports a match but no individual file scan confirms it, return None.

*   When the total accumulated byte size of members exceeds the module-level _BULK_SCAN_MAX_TOTAL constant, check_members must fall back to per-file scanning for remaining members to correctly attribute matches.

*   scan_archive(path, rules=None) must return a list of (member_path, [rule_identifiers]) tuples for each Python file in the archive that matched a YARA rule, where the second element is a list of rule identifier strings. It must support .whl/.zip and .tar.gz archive formats. It must only scan files with extensions .py, .pyc, .pyd, or .so. It must return [] for unsupported formats, corrupt archives, directory entries, non-file tar members, or when no rules are available.

*   scan_archive must use the module-level _rules when no explicit rules argument is provided, and must return [] when _rules is None.

*   The YARA rules stored in warehouse/utils/scanner_rules/ must detect PyArmor-encrypted content. The rule identifier must contain 'pyarmor_encrypted'. Detectable patterns include the main PyArmor executor call pattern and PyArmor runtime hook patterns (__pyarmor_enter__ and __pyarmor_exit__). Each rule must have a 'message' metadata field.

*   _is_valid_dist_file(filename, filetype, *, scan=True) must accept a keyword-only scan parameter. When scan=True and check_members returns a YaraMatch, _is_valid_dist_file must return (False, match.message). When scan=False, YARA scanning must be skipped entirely and check_members must not be called.

*   When _is_valid_dist_file returns (False, message) during a file upload, the file_upload handler must raise an HTTPBadRequest with HTTP status code 400, and the response status string must contain the failure message (e.g., 'PyArmor-encrypted content is not allowed').

*   The module-level constants _SCAN_MAX_FILE_SIZE, _BULK_SCAN_MAX_TOTAL, and the variable _rules in warehouse/utils/scanner.py must be module-level attributes that can be patched in tests.


*   Interface details: Type: Class
Name: YaraMatch
Location: warehouse/utils/scanner.py
Description: Frozen dataclass representing a YARA rule match found during archive scanning. Has three string fields: rule (the rule identifier that triggered), member (the archive member path that matched), and message (the user-facing message from the rule metadata).
Signature: YaraMatch(rule: str, member: str, message: str)

Type: Function
Name: compile_rules
Location: warehouse/utils/scanner.py
Signature: compile_rules(rules_dir=None) -> Optional[yara_x.Rules]
Description: Compiles all YARA rules found in the given directory (defaults to the built-in scanner_rules directory). Only globs *.yar files. Returns None if no .yar files are found, if any file fails to compile, or if any other error occurs (fail-open). All compiled rules must include a "message" metadata field.

Type: Function
Name: _get_rule_message
Location: warehouse/utils/scanner.py
Signature: _get_rule_message(rule) -> str
Description: Extracts and returns the string value of the "message" metadata field from a matched YARA rule object.

Type: Function
Name: check_members
Location: warehouse/utils/scanner.py
Signature: check_members(members, rules=None, *, archive_name="") -> Optional[YaraMatch]
Description: Scans an iterable of archive members for YARA rule matches. Each member is a tuple of (path_str: str, size: int, data: bytes). archive_name is a keyword-only argument. Falls back to the module-level _rules if rules is not specified. Skips files larger than _SCAN_MAX_FILE_SIZE. Returns a YaraMatch on the first matching member, or None for clean content. Returns None on yara_x.ScanError (fail-open). Avoids cross-boundary false positives by verifying individual file matches when doing bulk scanning. When total data exceeds _BULK_SCAN_MAX_TOTAL, falls back to per-file scanning.

Type: Function
Name: scan_archive
Location: warehouse/utils/scanner.py
Signature: scan_archive(path: str, rules=None) -> list[tuple[str, list[str]]]
Description: Opens the archive at path (supports .whl/.zip and .tar.gz formats) and returns a list of (member_path, [rule_identifiers]) tuples for every Python file that matched a YARA rule. The second element of each tuple is a list of rule identifier strings that matched for that member. Only scans files with extensions .py, .pyc, .pyd, or .so. Skips directory entries, non-file tar members, and files larger than _SCAN_MAX_FILE_SIZE. Returns [] for unsupported formats, corrupt archives, or when no rules are available. Uses the module-level _rules if rules is not explicitly provided.

Type: Constant
Name: _SCAN_MAX_FILE_SIZE
Location: warehouse/utils/scanner.py
Description: Integer constant controlling the maximum file size (in bytes) that will be scanned. Files exceeding this size are silently skipped. Must be a module-level attribute patchable in tests.

Type: Constant
Name: _BULK_SCAN_MAX_TOTAL
Location: warehouse/utils/scanner.py
Description: Integer constant controlling the total byte threshold for bulk scanning mode. When the accumulated size of members exceeds this value, check_members falls back to per-file scanning instead of bulk scanning to avoid cross-boundary false positives. Must be a module-level attribute patchable in tests.

Type: Variable
Name: _rules
Location: warehouse/utils/scanner.py
Description: Module-level variable holding the compiled YARA rules (or None if rules could not be compiled or none were found). Used by check_members and scan_archive as a fallback when no explicit rules argument is passed. Must be a module-level attribute patchable in tests.

Type: Function
Name: _is_valid_dist_file
Location: warehouse/forklift/legacy.py
Signature: _is_valid_dist_file(filename: str, filetype: str, *, scan: bool = True) -> tuple[bool, Optional[str]]
Description: Existing function extended with a keyword-only scan parameter. When scan=True (default), the function calls check_members on the archive's contents after all other validation. If check_members returns a YaraMatch, the function returns (False, match.message). When scan=False, YARA scanning is skipped entirely and the function does not call check_members. On success, returns (True, None).

Type: Function (modified behavior)
Name: file_upload
Location: warehouse/forklift/legacy.py
Signature: file_upload(request) -> Response
Description: Existing upload handler extended so that when _is_valid_dist_file returns (False, message), the handler raises an HTTPBadRequest with status code 400 and a status string that contains the validation failure message.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.