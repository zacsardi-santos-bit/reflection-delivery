Implement functionality to enhance the training CLI tools by enabling users to discover and download training example projects from a hosted repository catalog. Update the interactive checkpoint selection prompt to provide clearer instructions.

*   Implement the function `_get_all_train_init_example_options` in `truss/cli/train/core.py` with the following requirements:
    *   Make an HTTP GET request to `https://api.github.com/repos/basetenlabs/{repo_id}/contents/{examples_subdir}`.
        *   Default `repo_id` is 'ml-cookbook'.
        *   Default `examples_subdir` is 'examples'.
    *   Use headers `{}` if no token is provided, or `{'Authorization': 'token {token}'}` if a token is provided.
    *   Return a list of directory names (type == 'dir'), excluding files.
    *   Treat a single dict response as a one-element list before filtering.
    *   On `requests.exceptions.RequestException` or `requests.exceptions.HTTPError`, call `click.echo` with 'Error exploring directory: {error}. Please file an issue at https://github.com/basetenlabs/truss/issues' and return an empty list.

*   Implement the function `_get_train_init_example_info` in `truss/cli/train/core.py` with the following requirements:
    *   Make an HTTP GET request to `https://api.github.com/repos/basetenlabs/{repo_id}/contents/{examples_subdir}/{example_name}`.
        *   Use the same auth header logic as above.
        *   Default `repo_id` is 'ml-cookbook'.
        *   Default `examples_subdir` is 'examples'.
    *   Return the full API response as a list of dicts, wrapping a single dict in a list.
    *   On a 404 HTTP error, return an empty list silently.
    *   On other HTTP errors or `requests.exceptions.RequestException`, call `click.echo` with 'Error exploring directory: {error}. Please file an issue at https://github.com/basetenlabs/truss/issues' and return an empty list.

*   Implement the function `download_git_directory` in `truss/cli/train/core.py` with the following requirements:
    *   Create the target `local_dir` using `os.makedirs(local_dir, exist_ok=True)`.
    *   Make an HTTP GET request to `git_api_url` with headers `{}` if no token is provided, or `{'Authorization': 'token {token}'}` if a token is provided.
    *   Handle list and single-dict API responses by wrapping single dicts in a list.
    *   For items with `name=='training'` and `type=='dir'`, recursively call `download_git_directory` on that item's URL with the same `local_dir`.
    *   For other subdirectories, call `os.makedirs` with `os.path.join(local_dir, subdir_name)` and recurse into the item's URL.
    *   For file items, download using `download_url` and write content in binary mode to `open(os.path.join(local_dir, name), 'wb')`.
    *   Return `True` on success. On exceptions, print 'Error processing response: {error}' and return `False`.

*   Update the interactive checkbox prompt for checkpoint selection to display: 'Use spacebar to select/deselect checkpoints to deploy. Press enter when done.' This applies to all checkpoint types.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.