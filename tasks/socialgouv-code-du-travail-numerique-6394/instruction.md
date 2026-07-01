Implement a redesigned footer component that integrates a "need more information" section into the existing footer structure of the labor law application. Ensure the footer becomes a self-contained unit by consolidating the help section and addressing accessibility and security improvements.

*   Update the Footer component:
    *   Export the Footer component as a named export from `packages/code-du-travail-frontend/src/modules/layout/footer/index.tsx`.
    *   Render an informational banner section as the first child of the outer `<footer>` element with:
        *   `id="more-info"`.
        *   Heading: 'Besoin de plus d'informations ?'.
        *   Descriptive paragraph about labor ministry regional services.
        *   Link to `/besoin-plus-informations` with text 'Trouver les services près de chez moi'.
    *   Ensure the outer element is a plain `<footer>` tag without any class or id.
    *   Apply `fr-footer` class and `role="contentinfo"` to an inner `<div>`.
    *   Render the logo/brand element as a `<div class="fr-logo">` without wrapping it in an anchor link.
    *   Convert 'Accessibilité : partiellement conforme' in the footer bottom list to an `<a>` linking to `/mentions-legales`.

*   Secure external links:
    *   Add `rel="noopener noreferrer"` to all external links in the footer, including government domain links and the open licence link.

*   Reorganize components:
    *   Move `NeedMoreInfo` and `PopupContent` components from `src/modules/layout/infos/` to `src/modules/layout/footer/infos/`.
    *   Update any internal relative imports within these files to reflect the new directory depth.

*   Remove the old `src/modules/layout/Footer.tsx` file and ensure the Footer export is exclusively from `src/modules/layout/footer/index.tsx`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.