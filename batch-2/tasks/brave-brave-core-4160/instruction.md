Fix the badge update logic in the Brave Rewards browser extension to ensure that pending notifications are displayed consistently, regardless of publisher verification status. Implement the following changes to the `setBadgeText` function to achieve the correct badge behavior.

*   Update the `setBadgeText` function in `components/brave_rewards/resources/extension/brave_rewards/background/browserAction.ts` with the following logic:
    *   Always include `tabId` in the object passed to `chrome.browserAction.setBadgeText`.
        *   Pass `tabId` as `undefined` when no valid `tabId` is provided (i.e., when `tabId` is -1).
    *   When there are pending notifications (notification count > 0) and a valid `tabId`:
        *   Set the badge text to the numeric count of notifications (e.g., '1').
        *   Set the badge background color to `'#FB542B'` (orange).
    *   When `setBadgeText` is called without a valid `tabId` (i.e., `tabId` is -1 or omitted):
        *   Ensure the badge text object includes `tabId` as `undefined`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.