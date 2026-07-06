I'm working on improving the Python dependency graphing support in Dependabot.

*   The LockfileGenerator class must be located at python/lib/dependabot/python/dependency_grapher/lockfile_generator.rb and must be nested as Dependabot::Python::DependencyGrapher::LockfileGenerator.

*   LockfileGenerator must accept two keyword arguments in its constructor: dependency_files (array of DependencyFile objects) and credentials (array).

*   LockfileGenerator#generate must operate inside a temporary directory and run the shell command 'pyenv exec poetry config system-git-client true' (with fingerprint equal to the command string), followed by 'pyenv exec poetry lock --no-interaction' (with fingerprint equal to the command string).

*   LockfileGenerator#generate must write each dependency file to the temporary directory using its name as the path, and write a .python-version file containing the Python major.minor version from the LanguageVersionManager.

*   LockfileGenerator#generate must return a Dependabot::DependencyFile with name 'poetry.lock', the content read from the generated file, and directory set to the directory of the pyproject.toml dependency file (preserving subdirectories).

*   LockfileGenerator#generate must return nil and log an error message matching /Failed to generate poetry\.lock/ if the poetry lock shell command raises a HelperSubprocessFailed error.

*   LockfileGenerator#generate must return nil and log a warning with the exact message 'poetry.lock was not generated' if the poetry.lock file does not exist after the command runs.

*   Dependabot::Python::DependencyGrapher#resolved_dependencies must attempt to generate an ephemeral lockfile when no poetry.lock is present, by instantiating LockfileGenerator with dependency_files: and credentials: keyword arguments and calling generate.

*   When LockfileGenerator#generate returns nil, DependencyGrapher#resolved_dependencies must return dependencies with empty relationship arrays and PURLs without resolved versions (e.g., 'pkg:pypi/flask' not 'pkg:pypi/flask@3.1.3').

*   When LockfileGenerator#generate returns a DependencyFile, DependencyGrapher#resolved_dependencies must inject it and use it to resolve exact versions and full dependency relationships (e.g., 'pkg:pypi/flask@3.1.3' with its transitive dependencies).

*   Dependabot::Python::DependencyGrapher#relevant_dependency_file must return the pyproject.toml, not the ephemeral lockfile, even when an ephemeral lockfile was generated.

*   Dependabot::NpmAndYarn::DependencyGrapher#resolved_dependencies must only emit the warning log message matching /No lockfile was found/ when lockfile generation actually succeeds (returns a DependencyFile), and must NOT emit that warning when lockfile generation fails (returns nil).


*   Interface details: Type: Class
Name: LockfileGenerator
Location: python/lib/dependabot/python/dependency_grapher/lockfile_generator.rb
Description: Generates an ephemeral poetry.lock file for Python projects that have a pyproject.toml but no committed lockfile. Used by DependencyGrapher to enable full dependency relationship resolution when no lockfile is present.
Signature: initialize(dependency_files:, credentials:) -> void; generate() -> Dependabot::DependencyFile or nil

Type: Method
Name: generate
Location: python/lib/dependabot/python/dependency_grapher/lockfile_generator.rb (inside LockfileGenerator class)
Signature: generate() -> T.nilable(Dependabot::DependencyFile)
Description: Generates a poetry.lock file in a temporary directory. Returns a Dependabot::DependencyFile with name "poetry.lock", the generated content, and a directory matching the pyproject.toml's directory. Returns nil if the poetry lock command fails (catching SharedHelpers::HelperSubprocessFailed) or if the lockfile was not created. Logs an error matching /Failed to generate poetry\.lock/ on command failure, and logs a warning "poetry.lock was not generated" if the file is absent after running the command.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.