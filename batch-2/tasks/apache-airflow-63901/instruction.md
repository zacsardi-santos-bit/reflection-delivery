I work on a project that uses a CLI tool called Breeze to drive CI builds.

*   The ReproductionCommand dataclass must have two fields: argv (a list of strings representing the command tokens) and comment (an optional string defaulting to None).

*   should_print_local_reproduction() must return True only when both the CI environment variable equals 'true' and the GITHUB_ACTIONS environment variable equals 'true'. It must return False if either variable is absent, set to 'false', or set to any other value.

*   build_ci_image_reproduction_command() must return a ReproductionCommand with comment set to 'Build the CI image locally'. The argv must start with ['breeze', 'ci-image', 'build']. The '--github-repository' flag (and its value) must be appended only when github_repository differs from the default Apache Airflow repository ('apache/airflow'). The '--platform' and '--python' flags are always appended.

*   build_checkout_reproduction_commands() must read the GITHUB_REF and GITHUB_SHA environment variables. When GITHUB_REF matches the pattern 'refs/pull/{number}/{kind}' (where kind is 'merge' or 'head'), it must return two commands: the first with argv ['git', 'fetch', 'https://github.com/{github_repository}.git', '{GITHUB_REF}'] and comment 'Fetch the same code as CI (pull request {kind} ref) — or: gh pr checkout {number}'; the second with argv ['git', 'checkout', '{GITHUB_SHA}'] and comment None.

*   build_checkout_reproduction_commands() must return a single command with argv ['git', 'checkout', '{GITHUB_SHA}'] when GITHUB_REF is not set or is not a pull-request ref.

*   print_local_reproduction() must render the commands by calling get_console().print() from airflow_breeze.utils.console exactly 4 times when given a non-empty list: the first and fourth calls must each include a ruler string of 80 '─' characters; the third call must contain the rendered commands block where each command with a comment is preceded by a numbered heading '# N. {comment}' and the command argv is space-joined into a single shell string.

*   build_reproduction_command_from_context() must build the argv starting with the full command path obtained from the Click context (e.g. ['breeze', 'sub-cmd'] for nested commands).

*   build_reproduction_command_from_context() must exclude options that have expose_value=False — these never appear in the output argv.

*   build_reproduction_command_from_context() must exclude parameters whose name is in the set: 'verbose', 'dry_run', 'answer', 'include_success_outputs', 'debug_resources'. These must never appear in the output argv even when explicitly set.

*   build_reproduction_command_from_context() must include an option in the argv only when it was explicitly provided via command line, environment variable, or prompt (not when it was supplied by its declared default or default map). Exception: when an envvar sets an option to the same value as the declared default, the option must still be included.

*   build_reproduction_command_from_context() must always prefer the long-form option name (e.g. '--backend' over '-b', '--force' over '-f') when emitting options in the argv.

*   build_reproduction_command_from_context() must handle flag pairs (--flag/--no-flag): emit the positive long-form flag when the value is True and explicitly set; emit the negative long-form flag when the value is False and explicitly set; omit both flags when the value matches the default.

*   build_reproduction_command_from_context() must handle multiple-value options by repeating the flag for each value (e.g. ['--package-filter', 'foo', '--package-filter', 'bar']).

*   build_reproduction_command_from_context() must append positional arguments (click.Argument params) at the end of the argv, after all options.

*   build_reproduction_command_from_context() must convert non-string option values (e.g. integers) to strings in the argv.

*   build_reproduction_command_from_context() must use 'Run the same Breeze command locally' as its default comment value, and must accept an override via the comment keyword argument.

*   BreezeGroup must be a Click Group subclass located in airflow_breeze/utils/click_utils.py. When any command registered under a BreezeGroup is invoked in a GitHub Actions CI environment, it must call print_local_reproduction with a list whose last item is the ReproductionCommand built from the invoked command's Click context.


*   Interface details: Type: Class
Name: ReproductionCommand
Location: dev/breeze/src/airflow_breeze/utils/reproduce_ci.py
Description: A dataclass representing a single shell command for local reproduction.
Signature:
  argv: list[str]        # the command-line tokens (e.g. ["git", "checkout", "abc123"])
  comment: str | None = None  # optional human-readable comment shown before the command

Type: Function
Name: should_print_local_reproduction
Location: dev/breeze/src/airflow_breeze/utils/reproduce_ci.py
Signature: should_print_local_reproduction() -> bool
Description: Returns True only when both the CI and GITHUB_ACTIONS environment variables are set to "true" (case-insensitive comparison). Returns False in all other cases.

Type: Function
Name: build_ci_image_reproduction_command
Location: dev/breeze/src/airflow_breeze/utils/reproduce_ci.py
Signature: build_ci_image_reproduction_command(*, github_repository: str = <default_repo>, platform: str = "linux/amd64", python: str = "") -> ReproductionCommand
Description: Builds the command to build the CI image locally. The returned ReproductionCommand has comment="Build the CI image locally". The argv starts with ["breeze", "ci-image", "build"]; "--github-repository" and its value are appended only when github_repository differs from the default (apache/airflow) value; "--platform" and "--python" flags are always appended.

Type: Function
Name: build_checkout_reproduction_commands
Location: dev/breeze/src/airflow_breeze/utils/reproduce_ci.py
Signature: build_checkout_reproduction_commands(github_repository: str) -> list[ReproductionCommand]
Description: Builds git commands to check out the same code as CI. Reads GITHUB_SHA and GITHUB_REF environment variables. When GITHUB_REF matches the pattern "refs/pull/{N}/{kind}", returns two commands: (1) a git fetch command with comment "Fetch the same code as CI (pull request {kind} ref) — or: gh pr checkout {N}", argv=["git","fetch","https://github.com/{github_repository}.git","{GITHUB_REF}"]; (2) a git checkout command with comment=None, argv=["git","checkout","{GITHUB_SHA}"]. When GITHUB_REF is absent or not a PR ref, returns one command: argv=["git","checkout","{GITHUB_SHA}"].

Type: Function
Name: print_local_reproduction
Location: dev/breeze/src/airflow_breeze/utils/reproduce_ci.py
Signature: print_local_reproduction(commands: list[ReproductionCommand]) -> None
Description: Renders the reproduction commands to the console via get_console().print(). For a non-empty list of commands, makes exactly 4 calls to get_console().print(): (1) a top ruler line containing "─"*80; (2) a header line; (3) the rendered commands block (all commands concatenated, numbered comments as "# N. comment", argv joined as a shell string, each comment-having command starting a new numbered step); (4) a bottom ruler line containing "─"*80.

Type: Function
Name: build_reproduction_command_from_context
Location: dev/breeze/src/airflow_breeze/utils/reproduce_ci.py
Signature: build_reproduction_command_from_context(ctx: click.Context, *, comment: str = "Run the same Breeze command locally") -> ReproductionCommand
Description: Reconstructs the CLI invocation as a ReproductionCommand from a Click context. The argv starts with the full command path (e.g. ["breeze", "sub-cmd"]). Rules for options: (1) Options with expose_value=False are always excluded. (2) Options whose name is in the excluded set {"verbose", "dry_run", "answer", "include_success_outputs", "debug_resources"} are always excluded. (3) For simple boolean flags: included only when value is True AND explicitly set (command-line, env var, or prompt). (4) For flag pairs (--flag/--no-flag): when explicitly set, the appropriate side is emitted using the long-form name; omitted when at default. (5) For string/integer options: included only when explicitly set (not just default); value is converted to str. (6) For multiple-value options: the flag is repeated for each value. (7) Positional arguments are appended at the end. (8) Long-form option names are always preferred over short forms. When option is set via environment variable it is always included even if its value equals the declared default.

Type: Class
Name: BreezeGroup
Location: dev/breeze/src/airflow_breeze/utils/click_utils.py
Description: A Click Group subclass used for the Breeze CLI. When any command within this group is invoked while running in GitHub Actions CI (CI=true, GITHUB_ACTIONS=true), it automatically calls print_local_reproduction with a list of reproduction commands. The list ends with the reproduction of the invoked command built via build_reproduction_command_from_context.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.