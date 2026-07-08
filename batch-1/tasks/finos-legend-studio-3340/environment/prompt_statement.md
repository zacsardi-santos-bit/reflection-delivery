I'm deep in the query builder's filter panel and hit a real limitation: right now you can only compare a model property against a literal value, so there's no way to say "where property A equals property B". I want to be able to drag a property from the model explorer, or a column from the fetch structure panel, straight onto an existing filter condition and have it become the right-hand side of that comparison.

A few things that matter here. The drop zone indicator should only show up when the dragged item is type-compatible with the condition's left-hand side, and if it's incompatible the target area just shouldn't appear at all (no error, just don't render it). There also needs to be a reset button so I can clear out a property-based filter value and go back.

Some drag scenarios have to be blocked outright with clear warning messages, not silently swallowed: dragging a derivation column (whether someone's trying to make it a brand new filter condition or use it as a filter value), dragging a property onto a collection-based filter condition, and dragging collection-type properties in as filter condition values. Each of those needs its own appropriate warning/error text so it's obvious why it didn't work.

Also, oh, there's a nasty bug: when a derived property that requires parameters is used on both sides of a condition (left and right), setting the argument for one side currently overwrites the other. Each side needs to track its arguments completely independently so they don't stomp each other.

And last thing, when a filter condition has an empty string value and I switch the operator to a list-based comparison, it should cleanly convert that value into an empty list instead of sitting in some inconsistent half-state, and the query shouldn't be runnable until that validation issue is actually resolved.

Without two-property comparison people are stuck with literal-only filters, which blocks a lot of legit queries from being expressed in the UI, so the clear validation and independent argument tracking are what keep this predictable.
