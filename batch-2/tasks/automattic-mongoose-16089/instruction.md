I'm working with Mongoose and TypeScript and running into a problem with the types after populating references.

*   A new type `PopulatedDocumentMarker<PopulatedRawDocType, DepopulatedRawDocType>` must be declared in `types/populate.d.ts` within the mongoose module namespace and exported. It uses a unique symbol key to carry both the populated and depopulated raw doc types as invisible metadata. This type must be usable externally to strip the marker via `Omit<..., keyof PopulatedDocumentMarker<any, any>>`.

*   A new type `PopulateDocumentResult<Doc, Paths, PopulatedRawDocType, DepopulatedRawDocType>` must be declared in `types/populate.d.ts`. It merges the Doc with Paths (preserving the document methods) and attaches a `PopulatedDocumentMarker` carrying both the populated raw doc type and the depopulated raw doc type.

*   The `Document.populate()` instance method return type must be changed from `Promise<MergeType<this, Paths>>` to `Promise<PopulateDocumentResult<this, Paths, PopulatedPathsDocumentType<DocType, Paths>, DocType>>` in `types/document.d.ts`. The populated result must NOT be assignable to `HydratedDocFromModel<typeof Model>` (the original un-populated type).

*   The static `Model.populate()` method return type must be changed to return `Promise<PopulateDocumentResult<THydratedDocumentType, Paths, PopulatedPathsDocumentType<TRawDocType, Paths>, TRawDocType>>` for single-document overload, and the array overload must return the corresponding array type, in `types/models.d.ts`.

*   The query `populate()` result type in `types/query.d.ts` must be updated so that when the query result is a Document, it resolves to `PopulateDocumentResult<ResultType, Paths, MergeType<RawDocType, Paths>, RawDocType>` rather than `HydratedDocument<MergeType<...>>`. Chaining multiple `.populate<P>()` calls must accumulate the type information for all populated paths.

*   New overloads for `Document.toObject()` and `Document.toJSON()` must be added in `types/document.d.ts` that are selected when `this` has a `PopulatedDocumentMarker`. These must be placed before the existing overloads and must cover: (a) no options → returns populated raw doc type shape, (b) options without depopulate → applies transforms to populated raw doc type, (c) options with `depopulate: true` only → returns depopulated raw doc type shape, (d) options with `depopulate: true` plus other options → applies transforms to depopulated raw doc type.

*   When `toObject()` or `toJSON()` is called with no options on a populated document, the return type must reflect the populated subdocument shapes. For example, if `children` was populated from `Types.ObjectId[]` to `DocumentArray<HydratedDocFromModel<ChildModel>>`, then `toObject().children[0].name` must be typed as `string`.

*   When `toObject({ depopulate: true })` or `toJSON({ depopulate: true })` is called on a populated document, the return type must reflect the original depopulated schema. For example, `toObject({ depopulate: true }).children![0]` must be typed as `Types.ObjectId`.

*   When `toObject({ depopulate: true, flattenObjectIds: true })` is called on a populated document, the return type must reflect depopulated types with ObjectIds flattened to strings. For example, `toObject({ depopulate: true, flattenObjectIds: true }).children![0]` must be typed as `string`.

*   The `ApplyFlattenTransforms` type in `types/index.d.ts` must be updated to handle plain arrays (`T extends Array<infer ItemType>`) by recursing into the item type, returning `ApplyFlattenTransforms<ItemType, O>[]`. This case must appear after the `DocumentArray` case but before the `Subdocument` case.

*   When the `PopulatedDocumentMarker` is stripped from a populated document (e.g., via `Omit<typeof populatedDoc, keyof PopulatedDocumentMarker<any, any>>`), the `toObject()` call on the stripped type must fall back to the base (un-populated) type behavior, returning `Types.ObjectId` for array fields that were originally ObjectIds.


*   Interface details: Type: TypeAlias
Name: PopulatedDocumentMarker
Location: types/populate.d.ts
Signature: PopulatedDocumentMarker<PopulatedRawDocType, DepopulatedRawDocType>
Description: A type marker attached to populated document types. It carries both the populated raw document type and the original depopulated raw document type. Uses a unique symbol key so it is invisible at runtime. Must be exported from the mongoose module namespace. Used externally via `keyof mongoose.PopulatedDocumentMarker<any, any>` to strip the marker.

Type: TypeAlias
Name: PopulateDocumentResult
Location: types/populate.d.ts
Signature: PopulateDocumentResult<Doc, Paths, PopulatedRawDocType, DepopulatedRawDocType = PopulatedRawDocType>
Description: The result type of a populate operation. Combines the document with the populated paths (via MergeType) and attaches a PopulatedDocumentMarker carrying both the populated and depopulated raw doc types.

Type: TypeAlias
Name: PopulatedPathsDocumentType
Location: types/populate.d.ts
Signature: PopulatedPathsDocumentType<RawDocType, Paths>
Description: Helper type that computes the "raw doc type" for a document after population by merging (unpacking intersection of) RawDocType with the POJO form of the populated paths.

Type: TypeAlias
Name: PopulatePathToRawDocType
Location: types/populate.d.ts
Signature: PopulatePathToRawDocType<T>
Description: Recursive helper that converts a populate Paths type (which may contain Document instances, DocumentArrays, plain arrays, or nested objects) into a plain raw document type suitable for use as the populated raw doc type.

Type: TypeAlias
Name: ExtractDocumentObjectType
Location: types/populate.d.ts
Signature: ExtractDocumentObjectType<T>
Description: Extracts the plain object type from a Document intersection type. If T extends some ObjectType & Document, returns FlatRecord<ObjectType>; otherwise returns T.

Type: MethodOverloads
Name: Document.populate
Location: types/document.d.ts
Signature:
  populate<Paths = {}>(path: string | PopulateOptions | (string | PopulateOptions)[]): Promise<PopulateDocumentResult<this, Paths, PopulatedPathsDocumentType<DocType, Paths>, DocType>>;
  populate<Paths = {}>(path: string, select?: string | AnyObject, model?: Model<any>, match?: AnyObject, options?: PopulateOptions): Promise<PopulateDocumentResult<this, Paths, PopulatedPathsDocumentType<DocType, Paths>, DocType>>;
Description: Updated return type of the document instance populate method. Now returns PopulateDocumentResult instead of MergeType so that toObject()/toJSON() overloads can be resolved based on the marker.

Type: MethodOverloads
Name: Document.$assertPopulated
Location: types/document.d.ts
Signature: $assertPopulated<Paths = {}>(path: string | string[], values?: Partial<Paths>): PopulateDocumentResult<this, Paths, PopulatedPathsDocumentType<DocType, Paths>, DocType>
Description: Updated return type of $assertPopulated to attach the PopulatedDocumentMarker.

Type: MethodOverloads
Name: Document.toObject
Location: types/document.d.ts
Description: Four new overloads must be added BEFORE the existing overloads, conditioned on `this: PopulatedDocumentMarker<...>`:
  1. toObject<P, D>(this: PopulatedDocumentMarker<P, D>, options: { depopulate: true }): Default__v<Require_id<D>, TSchemaOptions>
  2. toObject<P, D, O extends ToObjectOptions & { depopulate: true }>(this: PopulatedDocumentMarker<P, D>, options: O): ToObjectReturnType<D, TVirtuals, O, TSchemaOptions>
  3. toObject<P, O extends ToObjectOptions>(this: PopulatedDocumentMarker<P, any>, options: O): ToObjectReturnType<P, TVirtuals, O, TSchemaOptions>
  4. toObject<P>(this: PopulatedDocumentMarker<P, any>): Default__v<Require_id<P>, TSchemaOptions>

Type: MethodOverloads
Name: Document.toJSON
Location: types/document.d.ts
Description: Four new overloads must be added BEFORE the existing overloads, conditioned on `this: PopulatedDocumentMarker<...>`. Same structure as toObject overloads:
  1. toJSON<P, D>(this: PopulatedDocumentMarker<P, D>, options: { depopulate: true }): Default__v<Require_id<D>, TSchemaOptions>
  2. toJSON<P, D, O extends ToObjectOptions & { depopulate: true }>(this: PopulatedDocumentMarker<P, D>, options: O): ToObjectReturnType<D, TVirtuals, O, TSchemaOptions>
  3. toJSON<P, O extends ToObjectOptions>(this: PopulatedDocumentMarker<P, any>, options: O): ToObjectReturnType<P, TVirtuals, O, TSchemaOptions>
  4. toJSON<P>(this: PopulatedDocumentMarker<P, any>): Default__v<Require_id<P>, TSchemaOptions>

Type: MethodOverloads
Name: Model.populate
Location: types/models.d.ts
Description: Both static Model.populate overloads must be updated to return PopulateDocumentResult instead of MergeType:
  - populate<Paths>(docs: Array<any>, options: ...): Promise<Array<PopulateDocumentResult<THydratedDocumentType, Paths, PopulatedPathsDocumentType<TRawDocType, Paths>, TRawDocType>>>
  - populate<Paths>(doc: any, options: ...): Promise<PopulateDocumentResult<THydratedDocumentType, Paths, PopulatedPathsDocumentType<TRawDocType, Paths>, TRawDocType>>

Type: TypeAlias
Name: ApplyFlattenTransforms (update)
Location: types/index.d.ts
Description: The ApplyFlattenTransforms conditional type must handle plain arrays (T extends Array<infer ItemType>) by recursing into the item type and returning ItemType[]. This case must be checked AFTER DocumentArray but BEFORE Subdocument. Without this, depopulate+flattenObjectIds on array fields will not correctly flatten the restored ObjectIds to strings.

Type: QueryPopulateResultType (update)
Location: types/query.d.ts
Description: The result type resolver for query populate (the conditional type that determines the result of Query.populate()) must be updated so that when the result is a Document, it returns PopulateDocumentResult<ResultType, Paths, MergeType<RawDocType, Paths>, RawDocType> instead of HydratedDocument<MergeType<RawDocType, Paths>, ...>. Same for array results.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.