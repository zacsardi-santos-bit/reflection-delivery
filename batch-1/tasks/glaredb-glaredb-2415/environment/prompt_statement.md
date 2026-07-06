I've noticed that when I drop a table in GlareDB, the table disappears from the catalog but its data files are still sitting on disk at the storage location. This means disk space never gets reclaimed — I can create and drop tables all day and the storage directory just keeps growing. I'd expect that dropping a table should also clean up the underlying files so the storage location stays tidy.

I also need the method that executes SQL on a local session to be publicly accessible, so I can write integration tests that exercise end-to-end behaviors like creating a table, inserting data, dropping it, and then verifying the files are gone.

Can you implement the physical file cleanup that runs after a table is dropped from the catalog, so that the table's storage directory is empty of data files after the drop completes?
