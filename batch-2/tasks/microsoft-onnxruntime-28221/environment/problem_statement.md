## Description

Static quantization requires calibration data to determine the appropriate scale and zero-point values for each tensor. Currently, every quantization run must rerun the full calibration process from scratch — even when the same model and calibration data are used repeatedly. This is wasteful and slow, especially during development when the quantization settings are being tuned.

We need a calibration cache system that allows calibration results to be saved to disk and reloaded on subsequent runs, so that users can skip recalibration when nothing has changed.

## Expected Behavior

- Calibration results for a model can be saved to a file and later loaded back, preserving all numeric data faithfully.
- When performing static quantization, users can supply a path to a cache file. If calibration data is also provided, results are saved to that path after calibration. If no calibration data is provided but a valid cache file exists, the cache is used instead of recalibrating.
- Providing neither calibration data nor a valid cache file should produce a clear error.
- The cache must record which calibration algorithm and which quantization mode were used. If the cache was produced with different settings than the current run, the system should warn the user and recompute rather than silently using incompatible cached data.
- Older cache files that predate the introduction of certain settings fields should be handled gracefully, with sensible defaults applied.

## Why This Matters

Without caching, developers who iterate on quantization settings must re-run the potentially expensive calibration step every time. With caching, calibration is done once and reused, making the workflow significantly faster.
