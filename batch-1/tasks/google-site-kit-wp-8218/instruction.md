Implement a new datastore module for audience management in the Analytics 4 module of Site Kit. Ensure it can fetch and create audience segments for a GA4 property, following established data store patterns.

*   Implement the `createAudience` action:
    *   Validate input and throw errors for:
        *   Non-object inputs with 'Audience must be an object.'
        *   Unrecognized keys with 'Audience object must contain only valid keys. Invalid key: "<key>"'
        *   Missing required keys with 'Audience object must contain required keys. Missing key: "<key>"'
        *   Non-array `filterClauses` with 'filterClauses must be an array with AudienceFilterClause objects.'
    *   On success, POST to `/google-site-kit/v1/modules/analytics-4/data/create-audience` with `{ data: { audience } }`.
    *   Update the store state to include the newly created audience.

*   Implement the `getAudiences` selector:
    *   Return `undefined` before the resolver fires.
    *   Make a GET request to `/google-site-kit/v1/modules/analytics-4/data/audiences` and return the audience list.
    *   Use existing store data if available, avoiding network requests.

*   Implement the `receiveGetAudiences` action:
    *   Accept an object with an 'audiences' array property.
    *   Populate the store state so `getAudiences` returns the data without fetching.

*   Export constants from `assets/js/modules/analytics-4/datastore/constants.js`:
    *   `AUDIENCE_FILTER_CLAUSE_TYPE_ENUM` with at least an `INCLUDE` value.
    *   `AUDIENCE_FILTER_SCOPE_ENUM` with at least an `AUDIENCE_FILTER_SCOPE_ACROSS_ALL_SESSIONS` value.

*   Export a fixture from `assets/js/modules/analytics-4/datastore/__fixtures__/index.js`:
    *   Named export `audiences`, an array of at least 3 audience objects.
    *   Ensure index 2 is a valid audience object.

*   Register the audiences datastore module on the `MODULES_ANALYTICS_4` store:
    *   Ensure actions and selectors are accessible through standard registry dispatch and select patterns.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.