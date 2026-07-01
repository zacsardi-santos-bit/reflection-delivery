I'm working with Realm Core and running into a data integrity issue when deleting objects whose links to other objects are stored inside mixed-type fields or collections. When I delete a source object (either directly or via batch deletion), the backlink counts on the linked destination objects don't get decremented to zero — they stay stale. Even worse, when I use recursive deletion on an object that points to another object through a mixed-type column, the transitively linked object isn't getting deleted, even though it should be since it has no other references.

This happens across different container shapes — whether the links are in a plain mixed-type column, a list of mixed values, a dictionary of mixed values, or nested mixed collections like lists-of-lists or dictionaries-of-dictionaries.

It also seems to break with large datasets (more than a thousand objects), likely because of how the internal cluster tree rebalances during random-order deletions.

The fix should ensure that when objects are deleted, any links held in mixed-type columns or mixed-type collections are properly cleaned up: backlinks are decremented, and recursively deleted objects that lose their last reference are also removed. After recursively deleting all source objects, both the source and destination tables should be completely empty.
