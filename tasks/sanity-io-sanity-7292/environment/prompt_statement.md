I'm working on the copy-paste feature in Sanity Studio and running into several issues that need to be fixed.

First, when a user copies a reference value and pastes it into a field, there's no validation that the referenced document actually exists — the paste goes through silently and can result in broken data. I need the paste operation to check that referenced documents exist and show a clear error message if they don't, without applying the change.

Second, pasting into array fields is broken in a few ways: I can't paste an object or reference into an array, and there's no support for appending a single item to an existing array instead of replacing the whole thing. When pasting something into an array it should replace the array by default, but there should also be an append mode that inserts the item at the end.

Third, when copying a reference between fields with different strength settings (one requires a weak reference, the other a strong one), the strength property should be adjusted automatically to match what the target field expects.

There's also a bug where the wrong error category is shown when array value types don't match — it shows a generic schema mismatch error instead of one specific to array value incompatibility.

Finally, the recent searches functionality stores and retrieves searches in a way that's tightly coupled to the UI hook, making the tests extremely flaky and hard to maintain. I'd like the storage logic extracted into its own module that can be tested in isolation and mocked independently. The stored format should be versioned (version 2), and each entry should record the query, the document type names, the filters, and a creation timestamp. Importantly, attempting to remove a search at an index that doesn't exist (whether too large or negative) should be a no-op — the storage should not be modified at all in those cases.
