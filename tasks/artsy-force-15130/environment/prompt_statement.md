I'm working on the artwork purchase sidebar and need to add a shipping estimate widget for works that support global shipping. Right now, buyers can't see any shipping cost estimates until they start the checkout process, which creates a lot of friction.

The idea is: when a specific feature flag is enabled, and the artwork is available to buy and ships internationally through our platform's logistics service, we should show a shipping estimate component inside the "Shipping and taxes" accordion section. If the artwork only ships domestically (not internationally), the estimate widget should not appear at all.

I also need to make sure the accordion tracking events for the "Shipping and taxes" section and the "Artsy Guarantee" section are firing correctly — they should emit an event with the section label and whether it was expanded or collapsed.

The shipping estimate component itself should live in the shared components folder and be a named export so it can be used from the sidebar.
