I'm working on the object storage layer of JuiceFS and need help cleaning up the test suite and adding some new features.

Right now, the tests have hardcoded endpoint URLs for each cloud storage provider baked directly into the test source. This makes it impossible to run the tests against a different environment without changing the code. I'd like all of those to be read from environment variables instead, with each test skipping cleanly when the relevant variable isn't set.

There's also a reliability issue with the SQL-backed object store: the database driver for SQLite is currently imported only in the test file, which means the implementation isn't self-contained. The driver should be imported in the implementation file itself, along with the drivers for MySQL and PostgreSQL, so the package works correctly in any context.

On top of that, I need to add support for four new cloud storage backends — EOS, Wasabi, SCS (Sina Cloud Storage), and IBM Cloud Object Storage. Each should follow the same constructor pattern as the existing providers.

Two older provider implementations (MSS and Yovole) should be removed since they're no longer being maintained or tested.

Finally, I'd like a test setup function that can read provider credentials from a local file at a well-known path, parse the key-value pairs from each line, and set them as environment variables before the tests run. This makes it easy to configure credentials locally without hardcoding anything.
