Implement a React hook named `useLocalizedUrl` that localizes URLs for Ledger's web properties based on the user's current language setting in the app. Ensure the hook reads the active locale from the Redux store and adjusts URLs accordingly for specific Ledger domains.

*   Create the `useLocalizedUrl` function in `apps/ledger-live-desktop/src/renderer/hooks/useLocalizedUrls/index.ts` with the signature `useLocalizedUrl(url: string): string`.
*   Use the Redux selector pattern to retrieve the current locale from the settings reducer.
*   For URLs on the `www.ledger.com` domain:
    *   Insert the locale code directly after the domain root.
    *   Map 'pt' to 'pt-br' and 'zh' to 'zh-hans'. Insert other locales (e.g., 'fr', 'ja') as-is.
*   For URLs on the `support.ledger.com` domain:
    *   Insert the locale code between '/hc/' and the rest of the path.
    *   Map 'fr' to 'fr-fr', 'en' to 'en-us', and 'zh' to 'zh-cn'.
*   For URLs on the `shop.ledger.com` domain:
    *   Insert the locale code directly after the domain root.
    *   Map 'pt' to 'pt-br'. Insert other locales (e.g., 'fr', 'es') as-is.
*   Return the original URL unchanged for any domain other than `www.ledger.com`, `support.ledger.com`, and `shop.ledger.com`.
*   Update the `troubleshootingUSB` constant in `apps/ledger-live-desktop/src/config/urls.ts` to use a locale-neutral base path: 'https://support.ledger.com/hc/articles/115005165269?utm_source=ledger_live_desktop&utm_medium=self_referral&utm_content=error'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.