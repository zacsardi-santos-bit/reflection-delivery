I'm working with a utility function that's supposed to populate empty fields in an object from a map of default values. The idea is that if a field is already set to something meaningful, the function should leave it alone — only unset (zero-value) fields should receive the default. But right now, the function seems to be overwriting fields that already have a value, which is not the intended behavior.

For example, if I have an object where one string field is already set to a non-empty value and I call this function to fill in defaults, I'd expect that existing value to be preserved. Same for integers — if a field already holds a non-zero number, it should not be replaced. And for booleans, if a field is already set to true, it should stay true even if the incoming map also says true.

Can you fix this so that the function only populates fields that are currently at their zero value, leaving already-populated fields untouched?
