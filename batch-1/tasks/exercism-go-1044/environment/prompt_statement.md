I'm working on adding a new exercise to the Go track on exercism. The exercise involves counting rectangles in an ASCII art grid. The grid is given as a list of strings, and I need to figure out how many complete rectangles are present.

A rectangle in this context is formed by corner markers at all four corners and edge characters running along the top, bottom, left, and right sides. Only fully formed rectangles should be counted — if a corner or edge character is missing, that shape doesn't qualify.

Some tricky cases I need to handle: grids with no shapes at all should return zero, small degenerate rectangles (just one unit wide or tall, or a single-cell square) should still be counted, and when multiple rectangles share edges or corners, each valid rectangle should be counted separately. Large complex grids with many overlapping rectangles should also be handled correctly.

I need to implement this so it passes all the test cases for this exercise.
