## Description

When deleting objects that have links to other objects stored in flexible mixed-type fields or collections, the database engine does not correctly clean up those links. Specifically, backlink counts on referenced objects are not decremented properly, and recursive deletion does not follow links embedded inside mixed-type columns. This causes data integrity issues — linked objects that should be deleted are left behind, and backlink tracking becomes stale.

## Expected Behavior

- When an object is deleted and it (or its embedded children) holds links to other objects via mixed-type columns or mixed-type collections (lists, dictionaries, or nested combinations thereof), the backlink counts on the referenced objects must be accurately updated to zero.
- When recursive object deletion is performed on an object whose mixed-type column contains a link to another object, the transitively linked object should also be deleted — provided it has no remaining references from other sources.
- If the transitively linked object still has other references (backlink count > 1), it should remain valid but have its backlink count decremented correctly.
- All of the above must work reliably even when the object cluster is large (larger than the internal B+ tree node size), including when cluster leaf nodes collapse or merge during random-order deletions.

## Why This Matters

Developers relying on Realm's object graph deletion and backlink tracking need consistent behavior regardless of how links are stored. Storing links in mixed-type columns is a valid and common pattern, but currently results in silent data integrity bugs when those objects are deleted. This fix ensures that the object lifecycle is managed correctly across all supported link storage formats.
