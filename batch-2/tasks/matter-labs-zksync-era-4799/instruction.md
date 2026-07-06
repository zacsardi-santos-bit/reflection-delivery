I'm working on the contract verifier in zksync-era and I need to make the compilation input builder aware of the specific compiler version being used.

*   The `ZkSolc::build_input` function must accept a second parameter `zksolc_version: &str` representing the raw version string of the zksolc compiler being used. The existing single-argument call sites in `lib.rs` must be updated to supply `&version.zk` as this second argument.

*   The `ZkSolc::is_post_1_5_0` function must be refactored from an instance method taking `&self` into an associated (static) function taking `zksolc_version: &str` and returning `bool`. It must be callable as `ZkSolc::is_post_1_5_0(version_str)`.

*   The `is_post_1_5_0` version check must implement the following logic: the special string `"vm-1.5.0-a167aa3"` must return `false`; for version strings with a `v` prefix (e.g. `"v1.5.0"`), strip the prefix and parse as semver; if the parsed version is >= 1.5.0 the function returns `true`, otherwise `false`; if semver parsing fails, the function returns `true`. Examples: `"vm-1.5.0-a167aa3"` → false; `"v1.5.0"`, `"v1.5.1"`, `"v1.10.1"`, `"v2.0.0"` → true; `"v1.4.15"`, `"v1.3.21"`, `"v0.5.1"` → false.

*   The `required_output_selection` helper in `ZkSolc` must accept an `is_post_1_5_0: bool` flag. When that flag is `true`, the output selector for contract outputs (`"*"` / `"*"` and file/contract-specific selectors) must include `"evm"` in addition to `"abi"`. When the flag is `false`, only `"abi"` should be added to those selectors (not `"evm"`). The `"*"` / `""` (file-level) selector must always include `"abi"` only, regardless of the flag.

*   When `ZkSolc::build_input` is called with a legacy zksolc version (pre-1.5.0), the resulting output selection must contain `"abi"` for contract selectors but must NOT contain `"evm"`. When called with a post-1.5.0 version, the output selection must contain both `"abi"` and `"evm"` for contract selectors.

*   Existing output selection entries provided in the incoming standard JSON request must be preserved and merged (not replaced) when `ZkSolc::build_input` builds its input. Required outputs are added only if not already present.

*   Root-level standard JSON settings fields (such as `suppressedWarnings`) provided in the incoming request must be preserved in the compiled input produced by `ZkSolc::build_input`.

*   The `Solc::build_input` function (for standalone EVM Solidity compilation, i.e. when no zksolc version is involved) must request explicit EVM bytecode output selectors: `"abi"`, `"evm.bytecode"`, and `"evm.deployedBytecode"`. It must NOT use the broad `"evm"` selector for these contracts.


*   Interface details: Type: Function
Name: ZkSolc::build_input
Location: core/lib/contract_verifier/src/compilers/zksolc.rs
Signature: build_input(req: VerificationIncomingRequest, zksolc_version: &str) -> Result<ZkSolcInput, ContractVerifierError>
Description: Constructs the zksolc compilation input from an incoming verification request. The second parameter is the raw version string of the zksolc compiler that will be used (e.g. "v1.5.0", "v1.3.13"). This version string is used to determine which output selectors to include. The function was previously called with only one argument; all call sites must be updated to supply the version string.

Type: Function
Name: ZkSolc::is_post_1_5_0
Location: core/lib/contract_verifier/src/compilers/zksolc.rs
Signature: is_post_1_5_0(zksolc_version: &str) -> bool
Description: Associated (static) function — NOT an instance method — that returns true if the given zksolc version string is v1.5.0 or newer. The special string "vm-1.5.0-a167aa3" must return false. Version strings with a "v" prefix are stripped before semver parsing. Versions that cannot be parsed as semver return true. Must be callable as ZkSolc::is_post_1_5_0("v1.5.0") without a ZkSolc instance.

Type: Function
Name: ZkSolc::required_output_selection
Location: core/lib/contract_verifier/src/compilers/zksolc.rs
Signature: required_output_selection(output_selection: Option<serde_json::Value>, file_name: &str, contract_name: &str, is_post_1_5_0: bool) -> serde_json::Value
Description: Builds the required output selection value by merging required outputs into any existing selection. When is_post_1_5_0 is true, adds "abi" and "evm" to the "*"/"*" and file/contract-specific selectors. When is_post_1_5_0 is false, adds only "abi". The "*"/"" (file-level) selector always gets "abi" only. Existing entries are preserved.

Type: Function
Name: Solc::build_input
Location: core/lib/contract_verifier/src/compilers/solc.rs
Signature: build_input(req: VerificationIncomingRequest) -> Result<SolcInput, ContractVerifierError>
Description: Builds the standalone Solc compilation input (no zksolc involved). For EVM contracts (compiler_zksolc_version is None), must add "abi", "evm.bytecode", and "evm.deployedBytecode" to the output selection. Must NOT add the broad "evm" selector. Existing output selections are preserved and merged.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.