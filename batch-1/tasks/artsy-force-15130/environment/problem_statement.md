## Description

When buyers view an artwork that is available for purchase and ships internationally through our logistics service, they currently have no way to see estimated shipping costs directly on the artwork page. They have to proceed further into the checkout flow before any shipping cost information is revealed. This creates friction and uncertainty for potential buyers.

## Expected Behavior

- When a feature flag is enabled, artworks that are available to buy and support international shipping through our shipping service should display a shipping estimate widget inside the "Shipping and taxes" section of the artwork sidebar.
- Artworks that only support domestic shipping (not international) should not show the shipping estimate widget — even when the feature flag is enabled.
- The existing "Shipping and taxes" and "Artsy Guarantee" accordion sections should continue to emit the correct tracking events when expanded or collapsed, with the appropriate expand state and section label.

## Why This Matters

Buyers need upfront visibility into shipping costs to make more informed purchasing decisions without having to navigate into the checkout flow. Surfacing a shipping estimate directly on the artwork page reduces uncertainty and should improve conversion for globally-shippable works.
