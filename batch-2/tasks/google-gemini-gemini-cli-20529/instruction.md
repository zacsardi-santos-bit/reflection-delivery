Update the truncation indicator in the CLI to include a hint about the keyboard shortcut for expanding hidden content. Ensure that the hint is appropriately formatted for both wide and narrow terminal displays, and that singular/plural forms are correctly used based on the number of hidden lines.

*   Implement the truncation indicator for non-narrow displays:
    *   For top overflow, display: '... first N lines hidden (Ctrl+O to show) ...'
    *   For bottom overflow, display: '... last N lines hidden (Ctrl+O to show) ...'
    *   Use 'line' when N is 1 and 'lines' when N is greater than 1.
*   Implement the truncation indicator for narrow-width displays:
    *   Use the format: '... N hidden (Ctrl+O) ...' for both top and bottom overflow.
*   Ensure the keyboard shortcut in the indicator is 'Ctrl+O'.
*   Verify that the bottom overflow indicator matches the regex: /^\.\.\. last \d+ lines? hidden \(Ctrl\+O to show\) \.\.\.$/

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.