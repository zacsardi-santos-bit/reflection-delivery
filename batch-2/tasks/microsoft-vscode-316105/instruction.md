I'm working on improving the Copilot chat quota notification experience in VS Code.

*   The ChatQuotaService constructor must accept a third parameter of type ICAPIClientService (imported from the endpoint/common/capiClient module), in addition to the existing IAuthenticationService and ILogService parameters.

*   The IChatQuotaService interface must declare a refreshQuota() method that returns Promise<void>. The ChatQuotaService implementation must provide this method.

*   On the first quota data arrival after sign-in or window reload, the system must store the current usage as a baseline WITHOUT showing any threshold notifications, even if the current usage already exceeds one or more thresholds.

*   Threshold notifications must only be triggered when quota usage crosses a threshold boundary — i.e., transitions from below a threshold to at or above it — after the baseline has been established. Thresholds already exceeded at baseline time must not trigger notifications.

*   When a threshold is crossed, refreshQuota() must be called (asynchronously) before the notification is displayed, meaning the notification display happens after the Promise resolves.

*   Sign-out must clear the stored baseline, so that the next sign-in re-establishes a fresh baseline on first data arrival (without triggering notifications).

*   The notification message for the 50% usage threshold must be exactly 'Credits at 50%'. For the 75% threshold it must be 'Credits at 75%'. For the 90% threshold it must be 'Credits at 90%'.

*   Session rate limit and weekly rate limit thresholds must follow the same baseline-first approach: first data arrival stores a baseline without notification; subsequent crossings trigger notifications.

*   The same threshold must not be re-shown once already triggered in a session; it is only re-shown after sign-out and sign-in resets the baseline.

*   When a higher threshold is crossed (e.g., 90% after already crossing 75%), a new notification must be shown with the higher threshold message.

*   Rate limit threshold warnings must still be suppressed when a quota threshold warning takes priority (quota threshold message takes precedence over rate limit message).


*   Interface details: Type: Class
Name: ChatQuotaService
Location: extensions/copilot/src/platform/chat/common/chatQuotaServiceImpl.ts
Description: Implementation of the chat quota service. Its constructor must now accept a third parameter of type ICAPIClientService (imported from extensions/copilot/src/platform/endpoint/common/capiClient.ts), in addition to the existing IAuthenticationService and ILogService parameters.
Signature: constructor(authService: IAuthenticationService, logService: ILogService, capiClientService: ICAPIClientService)

Type: Interface method
Name: refreshQuota
Location: extensions/copilot/src/platform/chat/common/chatQuotaService.ts (IChatQuotaService interface) and implemented in chatQuotaServiceImpl.ts (ChatQuotaService)
Description: A new method on the IChatQuotaService interface that refreshes quota data from the server on demand. Must return a Promise that resolves to void.
Signature: refreshQuota(): Promise<void>


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.