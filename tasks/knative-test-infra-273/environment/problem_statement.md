## Description

The testgrid integration package has a couple of issues that make it harder to use and less reliable for collecting test results.

First, the output filename is defined as a private constant, which means external packages that need to reference the output file (for reading, cleaning up, or validating) cannot do so without hardcoding the string themselves. This constant should be exported so it is part of the package's public API.

Second, the function that writes XML output to a file currently overwrites the file each time it is called. This means if multiple test suites are written during a single run, only the last one is preserved. The function should instead append to the file so that successive calls accumulate results rather than discarding previous ones.

Finally, the function that looks up the artifacts directory has no test coverage, so it's not clear whether it correctly falls back to a default value when the environment variable is not set.

## Expected Behavior

- The output filename should be accessible from outside the package.
- Writing XML output multiple times to the same directory should result in all outputs being present in the file (appending, not overwriting).
- The artifacts directory lookup should return the environment variable value when set, and fall back to a sensible default path when it is not.

## Why This Matters

Without these fixes, tools that rely on the testgrid package cannot reliably reference the output file by name, and any run that produces multiple test suites will silently lose all but the last result.
