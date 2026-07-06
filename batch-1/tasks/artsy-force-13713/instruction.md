Implement a new section in the app to display supplemental conditions of sale for auctions, including a main listing page and a detail page for individual agreements. Ensure the main page features a tabbed interface with "Current" and "Past" tabs, and the detail page shows the auction name and date range for a given sale agreement.

*   Update the `SaleAgreementsApp` component in `src/Apps/SaleAgreements/SaleAgreementsApp.tsx`:
    *   Accept a `viewer` prop (a Relay fragment reference).
    *   Render a tabbed interface with tabs labeled 'Current' and 'Past'.
    *   Default to the 'Current' tab, displaying sale agreements with status 'CURRENT'.
    *   Implement the 'Past' tab to display past sale agreements grouped by auction type:
        *   Use 'Partner Auctions: Benefit' for benefit auctions.
        *   Use 'Partner Auctions: Commercial' for commercial partner auctions.
        *   Use 'Artsy Auctions' for Artsy-run auctions.
        *   Only render section headings if there are agreements to display.
    *   Use the GraphQL fragment `SaleAgreementsApp_viewer` on the Viewer type to retrieve sale agreement data.

*   Update the `SaleAgreementRoute` component in `src/Apps/SaleAgreements/Routes/SaleAgreementRoute.tsx`:
    *   Accept a `saleAgreement` prop (a Relay fragment reference).
    *   Render the name of the associated sale.
    *   Use the GraphQL fragment `SaleAgreementRoute_saleAgreement` on the SaleAgreement type to retrieve sale agreement data.

*   Ensure sale agreement data includes at minimum: `internalID`, `displayStartAt`, `displayEndAt`, `published`, `status`, and the associated sale with `internalID` and `name`.

*   Implement the `SaleAgreementRoute_saleAgreement` GraphQL fragment in `src/Apps/SaleAgreements/Routes/SaleAgreementRoute.tsx`:
    *   Include fields: `internalID`, `displayStartAt`, `displayEndAt`, and `sale` (with `internalID` and `name`).

*   Implement the `SaleAgreementsApp_viewer` GraphQL fragment in `src/Apps/SaleAgreements/SaleAgreementsApp.tsx`:
    *   Retrieve `saleAgreementsConnection` edges with node fields: `internalID`, `displayStartAt`, `displayEndAt`, `published`, `status`, and `sale` (with `internalID`, `name`, and `isBenefit`).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.