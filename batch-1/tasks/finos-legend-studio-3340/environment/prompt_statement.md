I'm working on the query builder's filter panel and need to add support for using model properties as the right-hand side values of filter conditions, rather than being limited to literal values only. Right now you can only compare a property against a literal value, but you cannot compare one property against another property.

I need the filter panel to allow users to drag a property from the model explorer or a column from the fetch structure panel onto an existing filter condition to set another property as the comparison value. The drop zone should only appear when the dragged item is type-compatible with the filter condition, and an incompatible drag should simply not show the target area. There also needs to be a reset button to clear a property-based filter value.

Several drag scenarios need to be explicitly blocked with clear warning messages: dragging derivation columns (whether as a new filter condition or as a filter value), dragging a property onto a collection-based filter condition, and dragging collection-type properties as filter condition values.

Another issue I've noticed is that when a derived property requiring parameters is used on both sides of a filter condition (left and right), setting the argument for one side currently overwrites the other. Each side needs to track its arguments completely independently.

Finally, when a filter condition has an empty string value and the operator is switched to a list-based comparison, the value should be correctly converted to an empty list rather than leaving an inconsistent state, and the query should not be executable until the validation issue is resolved.
