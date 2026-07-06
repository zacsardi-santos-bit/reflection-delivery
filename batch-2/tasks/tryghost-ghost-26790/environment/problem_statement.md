## Description

The Transistor podcast embed card currently renders a static placeholder on the web — a generic icon with the text "Members-only podcasts" and a short description. This means visitors to a Ghost site cannot actually listen to Transistor podcasts inline; they only see a non-interactive placeholder. The embed should instead render a real podcast player so readers can listen directly on the page.

## Expected Behavior

- When a Transistor podcast card is rendered on the web, it should output an embeddable player (not a static placeholder)
- The embed should use lazy-loading so the player iframe does not block page load
- A no-JavaScript fallback should be provided, ensuring the podcast player is still accessible when JavaScript is disabled
- A small inline script should be included to detect the background color of the surrounding content and pass it to the player for visual consistency
- The site's unique identifier should be passed to the player as a context parameter so the player can tailor its experience per site; if no identifier is available, the parameter should simply be omitted

## Why This Matters

Ghost users who embed Transistor podcasts into their content currently see only a placeholder — their readers cannot actually interact with or listen to the podcast. Replacing the placeholder with a real player gives readers a seamless inline listening experience without leaving the page.
