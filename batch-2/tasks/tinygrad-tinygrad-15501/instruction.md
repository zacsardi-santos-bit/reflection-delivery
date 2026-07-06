I'm trying to write tests for the profiling CLI tool in our visualization utilities, but I'm running into a structural problem.

*   The argument parser setup in the profiling CLI tool must be refactored into a public function named `get_arg_parser` in `extra/viz/cli.py` that returns an `argparse.ArgumentParser`. The returned parser must support at minimum: `--profile` (boolean flag), `--profile-path` (path to a profile pickle file), and `--device` (string name of a device to inspect).

*   The `main` function in `extra/viz/cli.py` must accept a single positional argument `args` (a parsed argparse namespace) and must not call `sys.exit()` — it must return `None` after completing execution so callers can capture stdout and continue normally.

*   When `main` is called with `--profile` and `--profile-path` pointing to a valid profile pickle (but no `--device`), the output must list available device names. Device names that include the string 'SQTT' in them represent SQTT trace streams that can be queried individually.

*   When `main` is called with `--profile`, `--profile-path`, and `--device` set to an SQTT device name: the first line of output must contain the string 'Clk' (the header), and every line from index 2 onward (after the separator line at index 1) must have a numeric digit string as its first whitespace-separated token representing a clock timestamp.


*   Interface details: Type: Function
Name: get_arg_parser
Location: extra/viz/cli.py
Signature: get_arg_parser() -> argparse.ArgumentParser
Description: Returns the argument parser for the profiling CLI tool. The returned parser must support at minimum: --profile (boolean flag to enable profile viewing mode), --profile-path (path to a profile pickle file), and --device (string name of a specific device to inspect). This function must be publicly importable (not nested inside an `if __name__ == "__main__"` block).

Type: Function
Name: main
Location: extra/viz/cli.py
Signature: main(args) -> None
Description: The main entry point for the profiling CLI tool. Accepts a single argument `args` which is a parsed argparse namespace (e.g. the result of get_arg_parser().parse_args(...)). Must write output to stdout and return None — must not call sys.exit(). This function must be publicly importable.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.