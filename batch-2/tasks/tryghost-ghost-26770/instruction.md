I'm working on two related changes to the Ghost codebase.

*   The TransistorPodcastsAction component must render nothing (no 'Podcasts' heading, no 'View' link) when the hasPodcasts prop is false.

*   The TransistorPodcastsAction component must render nothing when the memberUuid prop is missing or empty.

*   When hasPodcasts is true and memberUuid is provided, the TransistorPodcastsAction component must render a 'Podcasts' heading and a 'View' link.

*   The default button text for the Transistor podcasts action (TRANSISTOR_DEFAULTS.button_text) must be 'View', replacing the previous default of 'Manage'.

*   The TRANSISTOR_DEFAULTS.url_template must be 'https://partner.transistor.fm/ghost/{memberUuid}'.

*   The 'View' link's href attribute must be the url_template with '{memberUuid}' substituted by the actual member UUID value.

*   The 'View' link must open in a new browser tab, indicated by target='_blank' and rel='noopener noreferrer' attributes.

*   A new component file must be created at apps/admin-x-settings/src/components/settings/membership/member-emails/welcome-email-customize-modal.tsx and be present on the filesystem.


*   Interface details: Type: Constant
Name: TRANSISTOR_DEFAULTS
Location: apps/portal/src/components/pages/AccountHomePage/components/transistor-podcasts-action.js
Description: Default configuration values for the Transistor podcasts integration. The button_text field must equal 'View'. The url_template field must equal 'https://partner.transistor.fm/ghost/{memberUuid}'.

Type: Component
Name: TransistorPodcastsAction
Location: apps/portal/src/components/pages/AccountHomePage/components/transistor-podcasts-action.js
Description: React component that renders the Transistor podcasts section for a member. Props: hasPodcasts (boolean), memberUuid (string), settings (object, optional). Returns null when hasPodcasts is false or memberUuid is missing. When rendered, displays a 'Podcasts' heading and a 'View' link whose href is derived from the url_template with {memberUuid} replaced by the actual member UUID. The link must have target='_blank' and rel='noopener noreferrer'.

Type: File
Name: welcome-email-customize-modal.tsx
Location: apps/admin-x-settings/src/components/settings/membership/member-emails/welcome-email-customize-modal.tsx
Description: New TSX file that must exist at this exact path. Must export a default component. This file is checked for existence as part of the test suite.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.