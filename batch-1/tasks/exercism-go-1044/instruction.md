Implement a function to count the number of complete rectangles in an ASCII art grid. The grid is represented as a slice of strings, with specific characters denoting corners and edges of rectangles.

*   Implement the `Count` function in the `rectangles` package.
    *   File location: `exercises/rectangles/rectangles.go`
    *   Function signature: `Count(lines []string) int`
*   Use the following characters to identify rectangles:
    *   '+' for corners
    *   '-' for horizontal edges
    *   '|' for vertical edges
*   Ensure the function returns:
    *   0 for an empty input slice, a slice containing only empty strings, or a grid with no valid rectangle-forming characters.
    *   The total count of all complete rectangles, including overlapping and nested rectangles.
*   Count degenerate rectangles:
    *   Rectangles of height 1 (e.g., two rows of '+--+' stacked).
    *   Rectangles of width 1 (e.g., two columns of '++' with '||' between).
    *   1x1 squares (e.g., adjacent '+' forming a 2x2 grid).
*   Do not count incomplete rectangles where any corner or required edge character is missing.
*   Handle large and complex grids efficiently, including those with up to 60 valid rectangles.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.