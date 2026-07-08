I'm building out the Analytics 4 module in Site Kit and I hit a gap: there's no way to fetch the list of audiences for a GA4 property or create new ones through the plugin's data store patterns. I want a new datastore module that plugs into the same conventions we use for other analytics resources so audience-segmentation UI work can move forward and creation is guarded before we make expensive API calls.

Two main capabilities here. First, a selector that retrieves the list of audiences for the property, and it should only fire a network request when the data isn't already loaded, if the audiences are already in the store don't request again. Second, an action to create a new audience by sending the audience definition to the right endpoint, and after a successful creation the new audience needs to be reflected in the store's audience state.

The create action has to validate its input before doing anything, so it should reject values that aren't objects with a clear error, reject objects with unrecognized property names (say which key is invalid), reject objects missing required properties (say which key is missing), and reject objects where the filter clauses property isn't an array.

Oh and I need two new constants exported from the existing analytics-4 constants file, one for the valid audience filter clause types and one for the valid audience filter scope values, so the rest of the codebase doesn't lean on raw strings. Also the datastore fixture file should include a sample list of at least three audience objects so tests can grab them by index.

Last thing, wire this new module into the existing combined store for the analytics-4 module so the actions and selectors are reachable through the standard registry dispatch and select patterns.
