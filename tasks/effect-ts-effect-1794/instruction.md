Implement a new schema combinator function named `eitherFromUnion` in the `Schema` module to handle untagged unions. This function should decode inputs into an `Either` value by attempting the right schema first, then the left schema as a fallback. It should also encode `Either` values back to the appropriate wire format using the respective schema.

Requirements:
*   Export the function `eitherFromUnion` from `packages/schema/src/Schema.ts`, accessible as `S.eitherFromUnion`.
*   Function signature: `eitherFromUnion<LI, L, RI, R>(left: Schema<LI, L>, right: Schema<RI, R>): Schema<LI | RI, Either<L, R>>`.
*   Accept two schema arguments: 
    *   `left`: Schema for the Left case.
    *   `right`: Schema for the Right case.
*   Decoding:
    *   Decode a union of the two schemas' encoded types into an `Either` value.
    *   Inputs matching the left schema decode to `E.left(decodedLeft)`.
    *   Inputs matching the right schema decode to `E.right(decodedRight)`.
    *   If both schemas can decode the same input, prioritize the right schema (producing `E.right`).
    *   Ensure the decoded result is a valid `Either` instance (satisfies `E.isEither`).
    *   On decoding failure, produce an error message formatted as:
        ```
        (rightEncodedType | leftEncodedType <-> Either<leftDecodedType, rightDecodedType>)
        └─ From side transformation failure
           └─ rightEncodedType | leftEncodedType
              ├─ Union member
              │  └─ Expected a rightType, actual value
              └─ Union member
                 └─ Expected a leftType, actual value
        ```
*   Encoding:
    *   Encode `E.left(value)` using the left schema.
    *   Encode `E.right(value)` using the right schema.
    *   On encoding failure, produce an error message formatted as:
        ```
        (wireFormat <-> Either<leftType, rightType>)
        └─ Transformation process failure
           └─ (SchemaName <-> DecodedType)
              └─ From side transformation failure
                 └─ SchemaName
                    └─ To side transformation failure
                       └─ Expected ExpectedType, actual actualValue
        ```
*   Support roundtripping: encoding an `Either` value and then decoding it must produce the original value.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.