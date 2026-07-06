Implement support for vendor-specific JSON content types in the OpenAPI-to-GraphQL schema converter. Ensure that the converter recognizes any content type ending with "json" as a JSON response body to successfully parse APIs like the Amadeus Hotel List API into a complete GraphQL schema.

*   Add a test data file:
    *   Place a valid OpenAPI 3.0 JSON specification for the Amadeus Hotel List API at `engine/crates/parser-openapi/test_data/amadeus.json`.
    *   Ensure the specification includes paths for searching hotels by hotel IDs, city code, and geocoordinates.

*   Modify the OpenAPI parser:
    *   Update the JSON content type detection in `engine/crates/parser-openapi/src/graph/operations.rs` to recognize any content type ending with "json" as a JSON body.
    *   Ensure the parser does not require an exact match against "application/json".

*   Ensure successful parsing:
    *   When parsing the Amadeus OpenAPI spec using `Format::Json` with `ApiMetadata` where `url` is `None`, the operation must complete without errors.

*   Exported GraphQL SDL requirements:
    *   Include enum types:
        *   AmadeusGetShoppingHotelsByCityAmenities (34 values including SWIMMING_POOL, SPA, FITNESS_CENTER, etc.)
        *   AmadeusGetShoppingHotelsByCityHotelSource (BEDBANK, DIRECTCHAIN, ALL)
        *   AmadeusGetShoppingHotelsByCityRadiusUnit (KM, MILE)
        *   AmadeusGetShoppingHotelsGeocodesAmenities (same 34 values)
        *   AmadeusGetShoppingHotelsGeocodesHotelSource (BEDBANK, DIRECTCHAIN, ALL)
        *   AmadeusGetShoppingHotelsGeocodesRadiusUnit (KM, MILE)
        *   AmadeusHotelUnitDistance (NIGHT, PIXELS, KILOGRAMS, POUNDS, CENTIMETERS, INCHES, BITS_PER_PIXEL, KILOMETERS, MILES, BYTES, KILOBYTES)
    *   Include object types:
        *   AmadeusHotel (fields: timeZoneName, subtype, name, lastUpdate, iataCode, hotelId, geoCode, distance, chainCode, address)
        *   AmadeusHotelAddress (countryCode: String)
        *   AmadeusHotelDistance (isUnlimited, displayValue, value: Float, unit: AmadeusHotelUnitDistance)
        *   AmadeusHotelGeoCode (longitude: Float, latitude: Float)
        *   AmadeusHotelSearchResponse (meta: AmadeusMeta, data: [AmadeusHotel!])
        *   AmadeusMeta (links: AmadeusMetaLinks, sort: [String!], count: Int)
        *   AmadeusMetaLinks (last, next, prev, first, self: String)
    *   Include a Query type with operations:
        *   shoppingHotelsByHotels(hotelIds: [String!]!): AmadeusHotelSearchResponse
        *   shoppingHotelsByCity with parameters hotelSource (default ALL), ratings, amenities, chainCodes, radiusUnit (default KM), radius (default 5), cityCode (required String)
        *   shoppingHotelsGeocodes with parameters hotelSource (default ALL), ratings, amenities, chainCodes, radiusUnit (default KM), radius (default 5), longitude (required Float), latitude (required Float)

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.