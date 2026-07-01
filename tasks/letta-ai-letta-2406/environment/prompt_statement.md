I'm trying to run tools in a local sandbox with a virtual environment, but some of my tools depend on Python packages that aren't bundled with the codebase. Right now there's no way to declare which packages should be installed when the virtual environment is set up — if a required package is missing, the tool just fails at runtime with a module not found error.

I'd like to be able to specify a list of pip packages (with optional version pinning) as part of the sandbox configuration so the virtual environment gets the right dependencies automatically. I'd also expect that creating a sandbox configuration without specifying a directory should work out of the box by falling back to a sensible default location.

Additionally, there should be an API endpoint where I can create or update a local sandbox configuration, including these package requirements, and get back the saved configuration with all the details I provided.
