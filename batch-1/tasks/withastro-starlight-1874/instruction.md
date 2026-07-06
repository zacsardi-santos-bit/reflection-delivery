Implement a feature to simplify sidebar configuration in Starlight by allowing page references via slugs, automatically using page titles as navigation labels. Ensure multilingual support and provide clear error messages for configuration mistakes.

*   Extend the sidebar configuration to accept:
    *   An object form `{ slug: string, label?: string, translations?: Record<string, string>, ... }`.
    *   A string shorthand treated as `{ slug: <string> }`.
*   Implement the `getSidebar` function in `packages/starlight/utils/navigation.ts` with the signature `getSidebar(pathname: string, locale: string | undefined): SidebarEntry[]`.
    *   Resolve slug-based items into link entries using the page's frontmatter title as the label.
    *   For multilingual sites, resolve slugs to locale-prefixed URLs and use translated titles for the current locale, falling back to the default locale's title if necessary.
    *   Allow optional `label` and `translations` to override the default title resolution.
*   Ensure `getSidebar` throws an `AstroError` for:
    *   Slugs starting or ending with a slash, with the message: `The slug \`"<slug>"\` specified in the Starlight sidebar config must not start or end with a slash.` and hint: `Please try updating \`"<slug>"\` to \`"<stripped-slug>"\`.`.
    *   Slugs not matching any entry in the docs content collection, with the message: `The slug \`"<slug>"\` specified in the Starlight sidebar config does not exist.` and hint: `Update the Starlight config to reference a valid entry slug in the docs content collection.\nLearn more about Astro content collection slugs at https://docs.astro.build/en/reference/api-reference/#getentry`.
*   Update the sidebar config schema in `packages/starlight/schemas/sidebar.ts` to include:
    *   The new object form and string shorthand for internal links.
    *   The `InternalSidebarLinkItem` type for the object-form internal link schema.
*   Ensure the config validation error message includes the new union variants with the type string: `{ link: string;  } | { items: array;  } | { autogenerate: object;  } | { slug: string } | string`.
*   Support slug-based items nested inside sidebar groups, resolving both object form and string shorthand correctly.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.