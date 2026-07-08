I'm ripping out the third-party write-ahead log dependency in FlyDB and need to build a bunch of foundational storage pieces on our own file I/O layer, since right now the WAL leans on an external library we're dropping and we've got zero support for column families or bloom filters. These are the base of our tiered storage so without them the db can't durably log mutations, carve data into namespaces, or use probabilistic structures to speed up lookups.

First thing, I need a custom WAL that takes config options for the storage directory path, file size, log count, and save interval, and can write both key-value records and delete records to a file-backed store. It's gotta survive hundreds of thousands of writes without erroring out, plus do periodic flushing to disk and directory cleanup.

Also want an in-memory table mapping string keys to byte-slice values with put, get, and delete, and when a key isn't there get should hand back a descriptive key-not-found error (clear message, not just nil).

Then a memory-backed db layer that stitches the WAL and the in-memory table together, handles big volumes of put and get, and exposes a way to pull all keys currently stored.

Oh and a bloom filter, init it with an expected item count and a target false-positive rate, add byte-slice items, and a membership check that definitively returns false for anything never added and true for stuff that was added.

Last, a column family abstraction that groups data into named logical namespaces. Each family needs create, drop, list, put, get, delete, and key enumeration, and creating one that already exists should return an error. All of it should round-trip arbitrary byte-slice values correctly, including structured query strings and the like.
