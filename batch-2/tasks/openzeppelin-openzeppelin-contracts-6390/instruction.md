I'm working with an ERC7579-compatible smart account that supports a hook module.

*   When uninstalling any module type other than the hook module type, if the hook's pre-check reverts, the transaction must revert and propagate the hook's error (with reason message 'preCheck reverts' from the mock hook).

*   When uninstalling any module type other than the hook module type, if the hook's post-check reverts, the transaction must revert and propagate the hook's error (with reason message 'postCheck reverts' from the mock hook).

*   When uninstalling the hook module itself and the hook's pre-check reverts, the uninstallation must still succeed: it must emit a ModuleUninstalled event with the hook module type ID and the hook's address, must NOT emit PreCheck or PostCheck events, and isModuleInstalled for the hook must return false afterward.

*   When uninstalling the hook module itself and the hook's post-check reverts, the uninstallation must still succeed: it must emit ModuleUninstalled, MUST emit a PreCheck event (because pre-check succeeded before the module was uninstalled), must NOT emit PostCheck, and isModuleInstalled for the hook must return false afterward.

*   When uninstalling the hook module itself and both pre-check and post-check revert, the uninstallation must still succeed: it must emit ModuleUninstalled, must NOT emit PreCheck or PostCheck, and isModuleInstalled for the hook must return false afterward.

*   When uninstalling the hook module itself and the hook contract has no code (e.g., a removed delegation), the uninstallation must still succeed: it must emit ModuleUninstalled, must NOT emit PreCheck or PostCheck, and isModuleInstalled for the hook must return false afterward.

*   The ERC7579HookMock contract must expose a revertOnPreCheck(bool) function that, when called with true, causes all subsequent preCheck calls to revert with the message 'preCheck reverts'.

*   The ERC7579HookMock contract must expose a revertOnPostCheck(bool) function that, when called with true, causes all subsequent postCheck calls to revert with the message 'postCheck reverts'.


*   Interface details: Type: Function
Name: _uninstallModule
Location: contracts/account/extensions/draft-AccountERC7579Hooked.sol
Signature: _uninstallModule(uint256 moduleTypeId, address module, bytes memory deInitData) internal virtual override
Description: Override of the base _uninstallModule that handles hook module uninstallation specially. When moduleTypeId equals MODULE_TYPE_HOOK, failures in the hook's preCheck or postCheck are tolerated and the uninstallation proceeds. For all other module types, hook check failures still propagate as reverts. Must emit ModuleUninstalled(moduleTypeId, module) on success (via the parent chain). The function must NOT use the withHook modifier directly — instead it must inline a variant of the hook logic that suppresses reverts when uninstalling the hook module itself.

Type: Function
Name: revertOnPreCheck
Location: contracts/mocks/account/modules/ERC7579Mock.sol
Signature: revertOnPreCheck(bool shouldRevert) external
Description: Added to ERC7579HookMock. When called with true, configures the mock hook so that all subsequent preCheck calls revert with the reason string "preCheck reverts". When called with false, preCheck proceeds normally. Default state is false (no revert).

Type: Function
Name: revertOnPostCheck
Location: contracts/mocks/account/modules/ERC7579Mock.sol
Signature: revertOnPostCheck(bool shouldRevert) external
Description: Added to ERC7579HookMock. When called with true, configures the mock hook so that all subsequent postCheck calls revert with the reason string "postCheck reverts". When called with false, postCheck proceeds normally. Default state is false (no revert).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.