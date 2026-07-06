Implement a React component named `ArtistMediumsTitle` that dynamically updates the browser tab title based on selected medium filters for an artist's works-for-sale page. Ensure the title reflects the active filters in a user-friendly format, both when filters are applied and when they are reset.

*   Create the `ArtistMediumsTitle` component in `src/Apps/Artist/Routes/WorksForSale/Components/ArtistMediumsTitle.tsx`.
    *   Signature: `ArtistMediumsTitle({ defaultTitle: string, name: string }): JSX.Element`
    *   Description: Updates the page `<title>` element based on selected artwork medium filters.
*   Handle medium filters:
    *   When no filters are selected (`additionalGeneIDs` is empty or undefined), render the `defaultTitle`.
    *   For a single medium filter, format the title as "{name} - {PluralMediumName} | Artsy".
    *   For two medium filters, format the title as "{name} - {First} and {Second} | Artsy", with mediums sorted alphabetically.
    *   For three or more medium filters, format the title as "{name} - {A}, {B}, and {C} | Artsy", using an Oxford comma and sorting alphabetically.
    *   When filters are reset to an empty array, revert to the `defaultTitle`.
*   On initial page load, if medium filters are present in the URL query parameters (`additional_gene_ids`), use them to set the title.
*   Ensure the component updates the page `<title>` element reactively as medium filters change in the `ArtworkFilterContext`.
*   Maintain a mapping for gene IDs to plural display names, including at least:
    *   "painting" → "Paintings"
    *   "sculpture" → "Sculptures"
    *   "photography" → "Photographs"

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.