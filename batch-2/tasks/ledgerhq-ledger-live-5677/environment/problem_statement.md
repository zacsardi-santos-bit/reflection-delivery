## Description

Ledger Live Desktop links users to various Ledger web properties — the main website, the support center, and the shop — but these links always point to English-language pages, regardless of the user's language setting in the app. A French, Japanese, Portuguese, or Chinese user who clicks a help link is silently sent to English content, which is a poor experience.

## Expected Behavior

- There should be a way to take any Ledger URL and automatically adjust it to the appropriate language version based on the user's currently active locale.
- The main Ledger website, support site, and shop each use their own URL structure for language variants, so the logic needs to produce the correct locale code per domain.
- Some languages require a different regional code than the short locale identifier used in the app (for example, a user with "Portuguese" selected should land on the Portuguese-Brazilian regional pages, and a "Chinese" user should land on the appropriate Chinese regional pages on each site).
- For URLs that belong to other web properties (not the main site, support site, or shop), the URL should be returned unchanged.

## Why This Matters

Users who have selected a non-English language in Ledger Live are consistently sent to English web pages when following help links or clicking promotional content. Automatically localizing these URLs ensures users land on content in their selected language, improving usability and reducing friction.
