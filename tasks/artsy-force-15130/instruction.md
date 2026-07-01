Implement a shipping estimate widget for artworks that support global shipping when a feature flag is enabled. Ensure the widget appears in the "Shipping and taxes" section of the artwork sidebar only for eligible artworks. Maintain correct tracking events for accordion sections.

*   Implement the `ArtsyShippingEstimate` component:
    *   Export as a named export from `src/Components/ArtsyShippingEstimate.tsx`.
    *   Accept an `artwork` prop of type `ArtsyShippingEstimate_artwork$key`.
    *   Render a shipping estimate widget.

*   Update `ArtworkSidebarFragmentContainer` in `src/Apps/Artwork/Components/ArtworkSidebar/ArtworkSidebar.tsx`:
    *   Import `ArtsyShippingEstimate` from `Components/ArtsyShippingEstimate`.
    *   Use `useFeatureFlag` from `System/Hooks/useFeatureFlag` to check the shipping estimate feature flag.
    *   Render `ArtsyShippingEstimate` in the "Shipping and taxes" section if:
        *   The feature flag is enabled.
        *   `artsyShippingDomestic` and `artsyShippingInternational` are true.
        *   The artwork is acquireable (`isAcquireable` is true).
    *   Do not render `ArtsyShippingEstimate` if `artsyShippingInternational` is false, regardless of other conditions.
    *   Include `artsyShippingDomestic` and `artsyShippingInternational` fields in the Relay GraphQL fragment.
    *   Include the `ArtsyShippingEstimate_artwork` fragment spread in the Relay GraphQL fragment.

*   Ensure tracking events for accordion sections:
    *   Emit a tracking event when the "Artsy Guarantee" accordion is toggled with:
        *   `action='toggledAccordion'`
        *   `context_module='artworkSidebar'`
        *   `context_owner_type='artwork'`
        *   `expand=true` (on open) or `expand=false` (on close)
        *   `subject='Be covered by the Artsy Guarantee when you check out with Artsy'`
    *   Emit a tracking event when the "Shipping and taxes" accordion is toggled with:
        *   `action='toggledAccordion'`
        *   `context_module='artworkSidebar'`
        *   `context_owner_type='artwork'`
        *   `expand=true` (on open) or `expand=false` (on close)
        *   `subject='Shipping and taxes'`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.