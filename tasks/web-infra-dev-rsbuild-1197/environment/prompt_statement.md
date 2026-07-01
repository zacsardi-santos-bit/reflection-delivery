I'm working on an Rsbuild project and I've run into a few related issues with how SVG minification and image compression work in end-to-end tests.

The SVG processing tests were previously set up with plugins configured inline inside a single shared test file. I want to split these into individual test files, each reading its plugin configuration from its own build config file in the test case directory — the same way a real project would work.

When SVG files with a viewBox are processed during the build, the viewBox attribute is being removed by the SVG optimizer. It should be preserved so that the rendered component has the correct width and height information.

Similarly, when an SVG contains elements with IDs (like a gradient), those IDs should be prefixed with a string derived from the source filename in the compiled output, so that multiple SVGs don't end up with conflicting element IDs.

For the image compression tests, instead of checking internal build statistics (which behave differently across bundlers), I'd like to directly compare the output file sizes to the original source files and verify they're smaller after compression. The compression should be configured in a dedicated build configuration file rather than passed inline in the test.

Can you help set up the build configuration files for each test case directory and ensure the SVG minification correctly preserves viewBox and adds filename-based ID prefixes?
