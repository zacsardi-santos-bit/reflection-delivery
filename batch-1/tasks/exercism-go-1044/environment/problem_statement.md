## Description

The Go track on exercism is missing a "rectangles" exercise. This exercise asks learners to count the number of complete rectangles that appear in an ASCII art grid.

## Expected Behavior

Given a grid of characters — where corners are marked with a specific symbol, horizontal edges with another, and vertical edges with another — the solution should correctly count all valid, complete rectangles present in the grid.

- An empty grid or a grid with no recognizable shapes should return a count of zero.
- Rectangles that share sides or corners should each be counted individually.
- Very small rectangles (1-unit wide or 1-unit tall, as well as single-cell squares) should be counted.
- An incomplete shape — one where a corner or edge character is missing — must not be counted.
- Large grids containing many overlapping rectangles should be handled correctly.

## Why This Matters

This exercise teaches spatial reasoning and string parsing, and is a valuable addition to the exercism Go track. Learners benefit from working through an algorithm that involves scanning a 2D structure for patterns with specific structural requirements.
