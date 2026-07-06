## Description

When a developer assigns an interactive ARIA role to a generic, non-semantic HTML element (for example giving a plain container element the visual or semantic appearance of a button), the element does not automatically become keyboard-focusable. Keyboard-only users and assistive technology users who navigate by keyboard can never reach that element, even though it is intended to be interactive.

Currently there is no lint warning to catch this pattern. Developers can ship code where elements appear interactive to screen readers but are completely unreachable via keyboard, violating accessibility guidelines.

## Expected Behavior

- An HTML element that is assigned an interactive ARIA role but has no mechanism to receive keyboard focus should produce an accessibility lint error explaining that the element is not focusable.
- The error should indicate which interactive role was found and suggest adding a focusability mechanism.
- Elements that already carry both an interactive ARIA role and explicit keyboard focusability should pass without a warning.
- Natively interactive elements (such as buttons and form controls that are inherently keyboard-reachable) should not be flagged.
- Elements assigned a non-interactive ARIA role should not be flagged.

## Why This Matters

Accessibility tools and screen readers may announce these elements as interactive, creating a confusing experience where a user hears about an interactive control they can never actually reach or activate with the keyboard. Catching this at lint time ensures the problem is fixed before users are affected.
