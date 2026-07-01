I'm working on cleaning up the SQL logic test setup for GlareDB. Right now the test runner is invoked from a nested subdirectory, so every test script that references a local data file has to navigate up two directory levels before reaching the testdata folder. It's messy and confusing.

I want to change things so the runner launches from the project root instead, meaning all those paths in the test scripts would just be root-relative, starting directly from the testdata folder. The test scripts cover a range of scan functions (parquet, CSV, JSON, delta, lance), direct file-path queries, glob patterns, file type inference, CTAS, views, and external tables — all of these need their paths updated to use the new root-relative style.

Can you make the necessary changes so that the test runner executes from the project root and update all the SQL logic test files to use the corrected relative paths?
