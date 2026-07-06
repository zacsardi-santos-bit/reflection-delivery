I'm working with a multi-language content setup and I've run into a bug with locale isolation.

*   When a localized tab contains a group field, and the document is created or updated with different locale values for that group field, each locale's data must be stored independently without overwriting other locales' values.

*   When fetching a document by ID with a specific locale, a group field nested inside a localized tab must return the value stored for that exact locale (e.g., fetching with the English locale returns the English value, fetching with the Spanish locale returns the Spanish value).

*   The Tab collection's 'tabLocalized' tab must include a field named 'group' of type group, containing a field named 'heading' of type text, to support locale-isolation testing for groups inside localized tabs.

*   The TypeScript type for Tab must include 'tabLocalized.group' as an optional object with an optional 'heading' string-or-null property.

*   The TypeScript select type for Tab ('TabsSelect') must include 'tabLocalized.group' as an optional field typed as either the generic boolean-like type parameter or an object with an optional 'heading' field of that same type.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.