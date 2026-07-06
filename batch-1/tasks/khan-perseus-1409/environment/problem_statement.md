## Description

The test builder utility for interactive graph questions has grown hard to use as we've added more graph types. The current API for setting correct-answer coordinates and starting positions uses positional array arguments in a way that makes it unclear whether you're configuring the starting layout or the expected answer. We also lack builder support for several graph types (points, angles), and there's no way to set custom question text or a background image directly through the builder.

Additionally, the default color for decorative locked figures (non-interactive reference points and lines drawn on the graph) is currently green, but the design intent is for these to appear in a neutral dark gray when no specific color has been chosen.

## Expected Behavior

- Each graph-type builder method should accept a named options object with clearly distinct fields for correct-answer coordinates and starting positions, rather than relying on argument position.
- The builder should expose methods for creating point-type graphs and angle-type graphs, which are currently not supported.
- A method to set the question's content text should be available.
- A method to configure a background image (URL, dimensions, and optional positioning/scale attributes) should be available.
- The default color for locked decorative points and lines should be a neutral dark gray rather than green.
- The default state of endpoint visibility on locked decorative lines should be hidden (not shown) by default.
- The default correct-answer coordinates for each graph type should represent more centered, realistic starting positions on the graph.

## Why This Matters

Test authors frequently need to configure interactive graph questions with specific starting and correct-answer states. The current inconsistent API — mixing positional arrays and named parameters across methods — leads to confusion and subtle bugs in test setup. Making the API consistent and adding missing graph type support will make it easier to write clear, correct tests for all interactive graph behavior.
