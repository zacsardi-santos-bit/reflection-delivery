## Description

The line and area chart components have two issues that need to be fixed:

1. **Line width is too thick**: The stroke width used for drawing series lines is currently too large, making lines appear overly bold and visually disproportionate. It should be reduced to a thinner value for a cleaner appearance.

2. **"Show dot" option doesn't render dots**: There is already an option to display circular markers at each data point on line and area charts. However, enabling this option currently has no visible effect — the dot markers are never actually added to the rendered output. The rendered models do not include a dot marker collection at all, meaning dot markers cannot be drawn regardless of the option setting.

## Expected Behavior

- Series lines should be drawn with a reduced stroke width.
- The rendered models for both line and area chart series components should always include a dot marker collection (empty when dots are disabled, populated when enabled).
- When the show-dot option is enabled, a circular marker should be rendered at each data point. Each marker should use the series color, a fixed radius, and the default style.
- Circle marker model objects in area charts should carry the series name so they can be properly identified during interactions.

## Why This Matters

Without these fixes, enabling the show-dot feature in line or area charts silently does nothing — users cannot see data point markers even when they've explicitly turned the option on. The incorrect line width also results in visually inconsistent charts.
