We have a mobile hybrid app where some users are administratively locked into the new app experience and should not be able to go back to the old app.

*   shouldUseOldApp must return false when tryNewDot.isLockedToNewApp is true, regardless of classicRedirect.dismissed value — mobile-locked users must be kept in NewApp.

*   isOldAppRedirectBlocked must return true when shouldRespectMobileLock is true and tryNewDot.isLockedToNewApp is true. It must return false when shouldRespectMobileLock is false, even if isLockedToNewApp is true — the mobile lock must not apply when shouldRespectMobileLock is false.

*   shouldHideOldAppRedirect must return true when shouldRespectMobileLock is true and isLoadingTryNewDot is true (loading in mobile-respecting context). It must also return true when isOldAppRedirectBlocked returns true. It must return false when shouldRespectMobileLock is false, even when tryNewDot is undefined and still loading.

*   shouldBlockOldAppExit(tryNewDot, isLoadingTryNewDot, shouldSetNVP) must return true when tryNewDot.isLockedToNewApp is true, regardless of other parameters. It must return true when the user is not locked, isLoadingTryNewDot is true, AND shouldSetNVP is true. It must return false when the user is not locked and either isLoadingTryNewDot is false or shouldSetNVP is false.

*   closeReactNativeApp must accept a params object with both shouldSetNVP (boolean) and isTrackingGPS (boolean) fields.

*   closeReactNativeApp must call shouldBlockOldAppExit passing the current tryNewDot state, current loading state, and shouldSetNVP as arguments. It must return early (not close the native app, not clear preloaded routes) when shouldBlockOldAppExit returns true.

*   When closeReactNativeApp is blocked (shouldBlockOldAppExit returns true) and isTrackingGPS is true, it must NOT invoke the GPS-in-progress modal handoff action.

*   closeReactNativeApp must maintain module-level Onyx subscriptions to NVP_TRY_NEW_DOT and IS_LOADING_APP to track the current tryNewDot and loading state for use in shouldBlockOldAppExit calls.

*   After a session switch (SESSION.accountID changes to a different value), closeReactNativeApp must re-block NVP-setting exits until the new user's NVP_TRY_NEW_DOT resolves. Once it resolves, exits must be allowed if there is no lock.

*   Auth token rotation (SESSION.authToken changes but SESSION.accountID remains the same) must not reset the resolved/unlocked state — closeReactNativeApp must continue to allow NVP-setting exits.

*   When shouldSetNVP is false and the user is not locked, closeReactNativeApp must call Navigation.clearPreloadedRoutes and invoke the native closeReactNativeApp passing {shouldSetNVP: false}.

*   BaseConfirmNavigateExpensifyClassicModal must hide the modal (pass false to isVisible) when shouldHideOldAppRedirect(tryNewDot, isLoadingTryNewDot, CONFIG.IS_HYBRID_APP) returns true, even when ONYXKEYS.IS_OPEN_CONFIRM_NAVIGATE_EXPENSIFY_CLASSIC_MODAL_OPEN is true. It must remain visible on web (CONFIG.IS_HYBRID_APP=false) when IS_OPEN_CONFIRM_NAVIGATE_EXPENSIFY_CLASSIC_MODAL_OPEN is true, even if isLockedToNewApp is true.

*   useRedirectToExpensifyClassic must compute canRedirectToExpensifyClassic as shouldRedirectToExpensifyClassic AND NOT shouldHideOldAppRedirect(tryNewDot, isLoadingTryNewDot, CONFIG.IS_HYBRID_APP). It must compute canUseAction as false only when shouldRedirectToExpensifyClassic is true but canRedirectToExpensifyClassic is false.

*   showRedirectToExpensifyClassicModal from useRedirectToExpensifyClassic must return early without opening a confirm modal when canRedirectToExpensifyClassic is false. It must open the confirmation modal when canRedirectToExpensifyClassic is true.

*   The TryNewDot Onyx type must include an optional isLockedToNewApp boolean field. When NVP_TRY_NEW_DOT is merged via Onyx with other data (e.g., classicRedirect fields), the isLockedToNewApp field must be preserved in the resulting stored object.


*   Interface details: Type: Function
Name: shouldUseOldApp
Location: src/libs/TryNewDotUtils.ts
Signature: shouldUseOldApp(tryNewDot: TryNewDot) => boolean | undefined
Description: Returns false when tryNewDot.isLockedToNewApp is true (mobile-locked users must stay in NewApp), even if classicRedirect.dismissed is also true. For unlocked users, follows existing classic redirect logic.

Type: Function
Name: isOldAppRedirectBlocked
Location: src/libs/TryNewDotUtils.ts
Signature: isOldAppRedirectBlocked(tryNewDot: OnyxEntry<TryNewDot>, shouldRespectMobileLock: boolean) => boolean
Description: Returns true when shouldRespectMobileLock is true and tryNewDot.isLockedToNewApp is true. Returns false when shouldRespectMobileLock is false, even if isLockedToNewApp is true — the mobile lock does not apply when shouldRespectMobileLock is false (e.g. web context).

Type: Function
Name: shouldHideOldAppRedirect
Location: src/libs/TryNewDotUtils.ts
Signature: shouldHideOldAppRedirect(tryNewDot: OnyxEntry<TryNewDot>, isLoadingTryNewDot: boolean, shouldRespectMobileLock: boolean) => boolean
Description: Returns true when shouldRespectMobileLock is true and isLoadingTryNewDot is true (hide redirect while loading in mobile context), OR when isOldAppRedirectBlocked returns true. Returns false when shouldRespectMobileLock is false, even when tryNewDot is still loading.

Type: Function
Name: shouldBlockOldAppExit
Location: src/libs/TryNewDotUtils.ts
Signature: shouldBlockOldAppExit(tryNewDot: OnyxEntry<TryNewDot>, isLoadingTryNewDot: boolean, shouldSetNVP: boolean) => boolean
Description: Determines whether an old-app exit action should be blocked. Returns true when tryNewDot.isLockedToNewApp is true, regardless of other parameters. Returns true when the user is not locked but isLoadingTryNewDot is true AND shouldSetNVP is true (block NVP-setting exits while the lock state is still resolving). Returns false when the user is not locked and either isLoadingTryNewDot is false or shouldSetNVP is false. This function is called by closeReactNativeApp passing shouldSetNVP (not CONFIG.IS_HYBRID_APP) as the third argument.

Type: Function
Name: closeReactNativeApp
Location: src/libs/actions/HybridApp/index.ts
Signature: closeReactNativeApp(params: {shouldSetNVP: boolean; isTrackingGPS: boolean}) => void
Description: Closes the React Native app by delegating to the native HybridApp module. Must call shouldBlockOldAppExit(currentTryNewDot, isLoadingTryNewDot, shouldSetNVP) first and return early if it returns true. Must not invoke setIsGPSInProgressModalOpen when the exit is blocked. When not blocked: if isTrackingGPS is true, calls setIsGPSInProgressModalOpen(true) instead of closing; otherwise calls Navigation.clearPreloadedRoutes() and invokes the native closeReactNativeApp with {shouldSetNVP}. Must maintain module-level state (currentTryNewDot, isLoadingTryNewDot) via Onyx subscriptions to NVP_TRY_NEW_DOT and IS_LOADING_APP. Must re-block exits after a session accountID change (detected via SESSION subscription) and re-allow once new NVP_TRY_NEW_DOT resolves. Auth token rotation for the same accountID must not reset the resolved state.

Type: Component
Name: BaseConfirmNavigateExpensifyClassicModal
Location: src/components/ConfirmNavigateExpensifyClassicModal/BaseConfirmNavigateExpensifyClassicModal.tsx
Description: A confirmation modal for navigating to Expensify Classic. Must set the ConfirmModal's isVisible prop to false (hide the modal) when shouldHideOldAppRedirect(tryNewDot, isLoadingTryNewDot, CONFIG.IS_HYBRID_APP) returns true, even when ONYXKEYS.IS_OPEN_CONFIRM_NAVIGATE_EXPENSIFY_CLASSIC_MODAL_OPEN is true. Must stay visible on web (CONFIG.IS_HYBRID_APP=false) when IS_OPEN_CONFIRM_NAVIGATE_EXPENSIFY_CLASSIC_MODAL_OPEN is true, regardless of isLockedToNewApp.

Type: Hook
Name: useRedirectToExpensifyClassic
Location: src/pages/inbox/sidebar/FABPopoverContent/useRedirectToExpensifyClassic.ts
Signature: useRedirectToExpensifyClassic() => { shouldRedirectToExpensifyClassic: boolean; canRedirectToExpensifyClassic: boolean; canUseAction: boolean; showRedirectToExpensifyClassicModal: () => Promise<void> }
Description: Hook managing the Expensify Classic redirect action for the FAB popover. canRedirectToExpensifyClassic must be shouldRedirectToExpensifyClassic AND NOT shouldHideOldAppRedirect(tryNewDot, isLoadingTryNewDot, CONFIG.IS_HYBRID_APP). canUseAction must be true when shouldRedirectToExpensifyClassic is false OR canRedirectToExpensifyClassic is true (i.e. false only when shouldRedirectToExpensifyClassic is true but canRedirectToExpensifyClassic is false). showRedirectToExpensifyClassicModal must return early without opening a confirm modal when canRedirectToExpensifyClassic is false.

Type: OnyxKey
Name: IS_OPEN_CONFIRM_NAVIGATE_EXPENSIFY_CLASSIC_MODAL_OPEN
Location: src/ONYXKEYS.ts
Description: Onyx key (boolean) that controls whether the ConfirmNavigateExpensifyClassicModal is open. Used by BaseConfirmNavigateExpensifyClassicModal.

Type: OnyxType
Name: TryNewDot
Location: src/types/onyx/TryNewDot.ts
Description: Must include an optional isLockedToNewApp boolean field. When NVP_TRY_NEW_DOT is merged via Onyx with other data (e.g., classicRedirect fields), the isLockedToNewApp field must be preserved in the resulting stored object (standard Onyx object merge behavior).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.