## Description

The site footer needs to be redesigned so that it directly integrates a "need more information" call-to-action section. Currently, this help section is positioned separately in the overall page layout, outside of the footer component itself. This makes the footer feel incomplete and forces the page layout to manage two separate bottom-of-page components. We want the footer to be a self-contained component that includes both the help prompt and the standard navigation/legal content.

## Expected Behavior

- The footer component should display a prominent invitation section at the top of the footer area, prompting users to seek help from local labor ministry services, with a direct link to the service locator page.
- The standard footer navigation, domain links, and legal items should appear below this new section, as before.
- The "accessibility compliance" statement currently displayed as plain text in the footer bottom bar should become a proper clickable link to the legal notices page.
- External links in the footer should include proper security attributes to protect users when navigating away.
- The footer component and its related sub-components (the informational section and its popup) should be grouped together in a dedicated subfolder within the layout module, rather than being spread across the layout root.

## Why This Matters

Moving the help section inside the footer reduces fragmentation in the layout layer and ensures every page consistently shows the call-to-action for finding local labor services as part of the footer experience. The accessibility and security improvements bring the footer in line with best practices.
