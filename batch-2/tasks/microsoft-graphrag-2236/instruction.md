I'm working on a vector store library and need to add metadata filtering, individual document lifecycle operations, and structured timestamp handling.

*   The graphrag_vectors.filtering module must export FilterExpr, Condition, AndExpr, OrExpr, NotExpr, Operator, and F.

*   The Operator enum must define values: eq, ne, gt, gte, lt, lte, and in_.

*   Condition must accept field (str), operator (Operator), and value (Any) and implement evaluate(record: dict) -> bool; evaluate must return False when the named field is absent from the record.

*   Condition.evaluate must compare correctly for all operators: eq (==), ne (!=), gt (>), gte (>=), lt (<), lte (<=), in_ (membership in list).

*   AndExpr must accept and_ (list of FilterExpr) and implement evaluate(record: dict) -> bool, returning True only when all sub-expressions evaluate to True.

*   OrExpr must accept or_ (list of FilterExpr) and implement evaluate(record: dict) -> bool, returning True when at least one sub-expression evaluates to True.

*   NotExpr must accept not_ (a single FilterExpr) and implement evaluate(record: dict) -> bool, returning the negation of the inner expression. Applying ~ to a NotExpr must return its inner expression (double-negation cancellation).

*   The & operator on filter expressions must produce an AndExpr with a flat list of sub-expressions: chaining (a & b) & c must yield a single AndExpr with 3 children, not nested AndExprs.

*   The | operator on filter expressions must produce an OrExpr with a flat list of sub-expressions: chaining (a | b) | c must yield a single OrExpr with 3 children.

*   The F builder must support attribute access (F.fieldname) and produce Condition objects via ==, !=, >, >=, <, <=, and an in_(list) method.

*   All filter expression types (Condition, AndExpr, OrExpr, NotExpr) must support Pydantic model_dump() serialization and model_validate() deserialization, surviving a JSON round-trip with correct field values and types preserved.

*   The graphrag_vectors.timestamp module must export explode_timestamp, _timestamp_fields_for, and TIMESTAMP_FIELDS.

*   explode_timestamp(timestamp_str, prefix) must parse an ISO 8601 datetime string and return a dict with exactly 7 keys: {prefix}_year (int), {prefix}_month (int), {prefix}_month_name (str), {prefix}_day (int), {prefix}_day_of_week (str), {prefix}_hour (int), {prefix}_quarter (int). It must return an empty dict for None or empty-string input.

*   Quarter assignment in explode_timestamp must follow: months 1-3 → 1, 4-6 → 2, 7-9 → 3, 10-12 → 4.

*   _timestamp_fields_for(prefix) must return a dict mapping the 7 timestamp component names to their type strings: year/month/day/hour/quarter → 'int', month_name/day_of_week → 'str'.

*   TIMESTAMP_FIELDS must be a combined dict containing exactly 14 entries — the 7 timestamp component fields for the 'create_date' prefix and the 7 for the 'update_date' prefix.

*   The VectorStore abstract base class must declare the following abstract methods with these signatures: insert(document: VectorStoreDocument) -> None, count() -> int, remove(ids: list[str]) -> None, update(document: VectorStoreDocument) -> None.

*   The VectorStore abstract methods similarity_search_by_vector, similarity_search_by_text, and search_by_id must accept select (list[str] | None = None), filters (FilterExpr | None = None), and include_vectors (bool = True) parameters.

*   LanceDBVectorStore must accept a fields parameter in its constructor — a dict mapping field names to type strings ('str', 'int', 'float', 'bool', 'date') — and use it to store typed metadata alongside each document.

*   LanceDBVectorStore.create_index() must create an empty collection with zero documents (not requiring a dummy document to be inserted and then deleted).

*   LanceDBVectorStore.insert(document) must add a single document to the store and automatically populate create_date if not provided.

*   LanceDBVectorStore.count() must return the number of documents currently in the store as an integer.

*   LanceDBVectorStore.remove(ids) must delete all documents whose ids appear in the list; searching for a removed id afterwards must raise IndexError.

*   LanceDBVectorStore.update(document) must update the stored document's fields; it must automatically set update_date to a non-None, non-'None' value after the update.

*   LanceDBVectorStore.search_by_id must raise IndexError when the requested id does not exist (rather than returning a placeholder document with a null vector).

*   When select is provided to similarity_search_by_vector, similarity_search_by_text, or search_by_id, only the named fields must appear in the returned document's data dict.

*   When include_vectors=False is passed to similarity_search_by_vector or search_by_id, the returned document's vector field must be None.

*   The filters parameter on similarity_search_by_vector and similarity_search_by_text must restrict results to documents that match the filter expression; results must remain ordered by similarity score.

*   Fields declared with type 'date' in the fields constructor parameter must be exploded into timestamp components in document.data (e.g. a field named published_at produces published_at_year, published_at_month, published_at_month_name, published_at_quarter, etc.), and those components must be filterable.

*   The built-in create_date field must be exploded into create_date_year, create_date_month, create_date_month_name, create_date_day, create_date_day_of_week, create_date_hour, and create_date_quarter in document.data, and those components must be filterable using the F builder.


*   Interface details: Type: Module
Name: graphrag_vectors.filtering
Location: packages/graphrag-vectors/graphrag_vectors/filtering.py
Description: Filter expression module. Must export: FilterExpr, Condition, AndExpr, OrExpr, NotExpr, Operator, F.

Type: Class
Name: Operator
Location: packages/graphrag-vectors/graphrag_vectors/filtering.py
Description: Enum of comparison operators.
Members: eq, ne, gt, gte, lt, lte, in_

Type: Class
Name: FilterExpr
Location: packages/graphrag-vectors/graphrag_vectors/filtering.py
Description: Abstract base class for all filter expressions. Supports & (produces AndExpr), | (produces OrExpr), ~ (produces NotExpr) operator overloads.

Type: Class
Name: Condition
Location: packages/graphrag-vectors/graphrag_vectors/filtering.py
Description: A single field comparison condition. Extends FilterExpr. Supports Pydantic model_dump() and model_validate().
Signature: Condition(field: str, operator: Operator, value: Any)
Method: evaluate(record: dict) -> bool  — returns False if field is absent; otherwise applies the operator comparison.

Type: Class
Name: AndExpr
Location: packages/graphrag-vectors/graphrag_vectors/filtering.py
Description: Logical AND of a list of filter expressions. Extends FilterExpr. Supports Pydantic model_dump() and model_validate(). When two AndExpr instances are combined with &, the result is a flat AndExpr (children are merged, not nested).
Signature: AndExpr(and_: list[FilterExpr])
Method: evaluate(record: dict) -> bool  — returns True only if all sub-expressions evaluate to True.

Type: Class
Name: OrExpr
Location: packages/graphrag-vectors/graphrag_vectors/filtering.py
Description: Logical OR of a list of filter expressions. Extends FilterExpr. Supports Pydantic model_dump() and model_validate(). When two OrExpr instances are combined with |, the result is a flat OrExpr (children are merged, not nested).
Signature: OrExpr(or_: list[FilterExpr])
Method: evaluate(record: dict) -> bool  — returns True if at least one sub-expression evaluates to True.

Type: Class
Name: NotExpr
Location: packages/graphrag-vectors/graphrag_vectors/filtering.py
Description: Logical NOT of a single filter expression. Extends FilterExpr. Supports Pydantic model_dump() and model_validate(). Applying ~ to a NotExpr returns its inner expression (double-negation cancellation).
Signature: NotExpr(not_: FilterExpr)
Method: evaluate(record: dict) -> bool  — returns the negation of the inner expression.

Type: Class
Name: F
Location: packages/graphrag-vectors/graphrag_vectors/filtering.py
Description: Fluent builder for constructing filter conditions. Attribute access (F.fieldname) creates a proxy that produces Condition objects via ==, !=, >, >=, <, <=, and in_(list).

Type: Module
Name: graphrag_vectors.timestamp
Location: packages/graphrag-vectors/graphrag_vectors/timestamp.py
Description: Timestamp explosion utilities. Must export: explode_timestamp, _timestamp_fields_for, TIMESTAMP_FIELDS.

Type: Function
Name: explode_timestamp
Location: packages/graphrag-vectors/graphrag_vectors/timestamp.py
Signature: explode_timestamp(timestamp_str: str | None, prefix: str) -> dict
Description: Parses an ISO 8601 datetime string and returns a dict with 7 component keys: {prefix}_year (int), {prefix}_month (int), {prefix}_month_name (str), {prefix}_day (int), {prefix}_day_of_week (str), {prefix}_hour (int), {prefix}_quarter (int). Returns empty dict {} for None or empty-string input. Quarter mapping: months 1-3 → 1, 4-6 → 2, 7-9 → 3, 10-12 → 4.

Type: Function
Name: _timestamp_fields_for
Location: packages/graphrag-vectors/graphrag_vectors/timestamp.py
Signature: _timestamp_fields_for(prefix: str) -> dict
Description: Returns a dict mapping the 7 timestamp component field names (for the given prefix) to their type strings: {prefix}_year → 'int', {prefix}_month → 'int', {prefix}_month_name → 'str', {prefix}_day → 'int', {prefix}_day_of_week → 'str', {prefix}_hour → 'int', {prefix}_quarter → 'int'.

Type: Constant
Name: TIMESTAMP_FIELDS
Location: packages/graphrag-vectors/graphrag_vectors/timestamp.py
Description: Combined dict containing exactly 14 entries — the 7 timestamp component field definitions for the 'create_date' prefix merged with the 7 for the 'update_date' prefix.

Type: Class
Name: VectorStore
Location: packages/graphrag-vectors/graphrag_vectors/vector_store.py
Description: Abstract base class for vector store implementations. Must declare new abstract methods and updated method signatures.
Signatures:
  insert(document: VectorStoreDocument) -> None
  count() -> int
  remove(ids: list[str]) -> None
  update(document: VectorStoreDocument) -> None
  similarity_search_by_vector(query_embedding: list[float], k: int = 10, select: list[str] | None = None, filters: Any = None, include_vectors: bool = True) -> list[VectorStoreSearchResult]
  similarity_search_by_text(text: str, text_embedder: TextEmbedder, k: int = 10, select: list[str] | None = None, filters: Any = None, include_vectors: bool = True) -> list[VectorStoreSearchResult]
  search_by_id(id: str, select: list[str] | None = None, include_vectors: bool = True) -> VectorStoreDocument

Type: Class
Name: LanceDBVectorStore
Location: packages/graphrag-vectors/graphrag_vectors/lancedb.py
Description: LanceDB implementation of VectorStore with filtering, field projection, timestamp explosion, and full CRUD operations.
Signature: LanceDBVectorStore(db_uri: str, index_name: str, vector_size: int, fields: dict[str, str] | None = None)
Methods:
  connect() -> None
  create_index() -> None  — creates an empty table with zero documents
  insert(document: VectorStoreDocument) -> None  — inserts one document; auto-sets create_date
  count() -> int
  remove(ids: list[str]) -> None  — deletes documents; search_by_id for a removed id raises IndexError
  update(document: VectorStoreDocument) -> None  — updates fields; auto-sets update_date to non-None value
  load_documents(documents: list[VectorStoreDocument]) -> None
  search_by_id(id: str, select: list[str] | None = None, include_vectors: bool = True) -> VectorStoreDocument  — raises IndexError for non-existent ids
  similarity_search_by_vector(query_embedding: list[float], k: int = 10, select: list[str] | None = None, filters: Any = None, include_vectors: bool = True) -> list[VectorStoreSearchResult]
  similarity_search_by_text(text: str, text_embedder: Any, k: int = 10, select: list[str] | None = None, filters: Any = None, include_vectors: bool = True) -> list[VectorStoreSearchResult]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.