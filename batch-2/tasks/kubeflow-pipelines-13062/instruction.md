I noticed that the navigation buttons in the sidebar are all rendered with the application's primary accent color.

*   The SideNav component must render all navigation buttons (including 'Pipelines' and 'Documentation') using the MUI CSS class 'MuiButton-textInherit' instead of 'MuiButton-textPrimary'. Setting the button color to 'inherit' (rather than 'primary') achieves this.

*   The SideNav component's navigation buttons must NOT have the CSS class 'MuiButton-textPrimary' applied.

*   The 'Documentation' navigation item in SideNav must be rendered as a button wrapped inside an anchor element whose 'href' attribute is set to ExternalLinks.DOCUMENTATION, whose 'target' attribute is '_blank', and whose 'rel' attribute is 'noopener noreferrer'.

*   ExternalLinks must be exported from frontend/src/components/Router.tsx (alongside the already-exported RoutePage) and must include a DOCUMENTATION property containing the documentation URL string.


*   Interface details: Type: Object/Enum
Name: ExternalLinks
Location: frontend/src/components/Router.tsx
Description: An exported object (or enum) containing named constants for external URLs used in the application. Must be exported alongside the existing RoutePage export. Must include at minimum a DOCUMENTATION property whose value is the documentation URL string.
Signature: export const ExternalLinks = { DOCUMENTATION: string, ... }


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.