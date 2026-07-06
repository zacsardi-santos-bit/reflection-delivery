Implement a fix in Realm Core to ensure proper cleanup of links stored in mixed-type fields or collections when objects are deleted. Ensure that backlink counts are decremented correctly and that transitively linked objects are deleted when they lose their last reference.

*   Ensure that when an object containing embedded objects is batch-erased, any typed links (ObjLink values) within Mixed-typed list columns have their backlink counts decremented to zero on the referenced objects.
*   Implement recursive object removal such that:
    *   When an object with a Mixed-typed column containing a typed link is deleted, both the source and the transitively linked destination object are removed, leaving both tables empty.
    *   Support typed links stored in Mixed columns of all container shapes: plain Mixed column, list of Mixed values, dictionary of Mixed values, nested list, nested dictionary, lists-of-lists, and dictionaries-of-dictionaries.
*   Handle source objects linking to destination objects via any supported Mixed container:
    *   If the destination object had exactly one backlink (from the removed source), delete the destination object.
    *   If the destination object had more than one backlink, decrement its backlink count by one.
*   Ensure correct backlink count decrements to zero when removing objects from large clusters (e.g., 2000 or more objects) containing Mixed-typed links, even during cluster leaf collapse and join operations in random-order removals.
*   Verify that after all source objects in a table are removed via recursive deletion, both the source and destination tables are completely empty.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.