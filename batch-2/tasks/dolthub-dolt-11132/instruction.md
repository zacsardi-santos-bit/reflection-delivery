I'm running into a nasty data corruption bug that affects tables created on an older version of Dolt after upgrading to a newer one.

*   Must implement a function PreserveAdaptiveEncoding(existing TypeInfo, fresh TypeInfo) TypeInfo in the go/libraries/doltcore/schema/typeinfo package that preserves the persisted storage encoding from an existing column's TypeInfo onto a fresh TypeInfo when both belong to the same type family.

*   When the existing argument is nil, PreserveAdaptiveEncoding must return the fresh TypeInfo unchanged without modification.

*   When the fresh argument is nil, PreserveAdaptiveEncoding must return nil.

*   When existing and fresh are both string/text family types (blobStringType) and existing was persisted under the legacy address encoding (val.StringAddrEnc), PreserveAdaptiveEncoding must return fresh with val.StringAddrEnc applied — not the global val.StringAdaptiveEnc default — even when the global UseAdaptiveEncoding flag is true. The SQL type semantics of fresh (e.g. LONGTEXT MaxCharacterLength) must remain unchanged.

*   When existing and fresh are both binary/blob family types (varBinaryType) and existing was persisted under val.BytesAddrEnc, PreserveAdaptiveEncoding must return fresh with val.BytesAddrEnc applied.

*   When existing and fresh are both JSON type and existing was persisted under val.JSONAddrEnc, PreserveAdaptiveEncoding must return fresh with val.JSONAddrEnc applied (not val.JsonAdaptiveEnc).

*   When existing and fresh are the same concrete geometry type (any of: pointType, linestringType, polygonType, multipointType, multilinestringType, multipolygonType, geomcollType, geometryType) and existing was persisted under val.GeomAddrEnc, PreserveAdaptiveEncoding must return fresh with val.GeomAddrEnc applied (not val.GeomAdaptiveEnc).

*   When existing and fresh belong to different type families (e.g. varStringType vs blobStringType for VARCHAR→TEXT, or pointType vs linestringType for POINT→LINESTRING), PreserveAdaptiveEncoding must return fresh unchanged — it must not apply the old encoding across a family boundary.

*   When the type family does not use adaptive encoding (e.g. integer types), PreserveAdaptiveEncoding must return fresh unchanged.

*   An ALTER TABLE MODIFY COLUMN statement that widens a text column (e.g. TEXT to LONGTEXT) on a table originally created while the global adaptive encoding flag was false must preserve the column's original val.StringAddrEnc encoding in the persisted schema, even when the global adaptive encoding flag is true at the time of the ALTER.

*   An ALTER TABLE DROP COLUMN statement executed while the global adaptive encoding flag is true must not change the persisted storage encoding of surviving columns that were originally created with the global adaptive encoding flag false (i.e., their val.StringAddrEnc encoding must be retained).

*   When reading text or blob column values from tables created by an older compatible version of Dolt, the output must not contain panic indicators such as messages about invalid hash lengths, recovered panics, or runtime errors.


*   Interface details: Type: Function
Name: PreserveAdaptiveEncoding
Location: go/libraries/doltcore/schema/typeinfo/
Signature: PreserveAdaptiveEncoding(existing TypeInfo, fresh TypeInfo) TypeInfo
Description: Returns the fresh TypeInfo with its storage encoding pinned to the existing TypeInfo's encoding, when both represent the same type family. This prevents a freshly-constructed TypeInfo from silently adopting the global adaptive-encoding default when the column was originally persisted under a legacy address encoding. Acts as a no-op (returns fresh unchanged) when existing is nil, when fresh is nil (returns nil), when the two TypeInfos belong to different type families (e.g. VARCHAR to TEXT, Point to LineString), or when the type family does not use variable adaptive encoding (e.g. integer types). Covered type families: string/text (blobStringType), binary/blob (varBinaryType), JSON (jsonType), and all concrete geometry types (pointType, linestringType, polygonType, multipointType, multilinestringType, multipolygonType, geomcollType, geometryType).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.