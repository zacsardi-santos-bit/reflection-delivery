I'm working on improving spec compliance for the internationalization built-ins in this JavaScript engine. There are two missing features I need to add.

First, when I create a plural rules formatter and call the method to get its resolved options, the returned object is missing a required formatting style field. According to the ECMAScript Internationalization specification, this field should always be present in the resolved options, and it should default to the basic decimal notation style when no explicit option is provided.

Second, the locale prototype object is missing an accessor property for locale variant subtag information. The spec requires that this property be accessible on the locale prototype, but right now checking for the presence of this accessor on the locale prototype returns false, which breaks any code that relies on feature-detecting this property.

Both of these are internationalization spec compliance gaps — the APIs exist, but they're returning incomplete data or missing required properties.
