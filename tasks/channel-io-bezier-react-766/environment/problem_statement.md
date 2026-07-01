# Add support for 72px Avatar size and fix status indicator positioning

## Description

The Avatar component currently positions the online/offline status indicator dot using an internal size scale that is separate from the avatar's actual pixel dimensions. This creates a mismatch: the boundary between "compact" and "spacious" indicator positioning is defined in terms of a different unit system, making it hard to reason about and error-prone when adding new avatar sizes.

We need to add a new 72-pixel avatar size option and, as part of that work, align the status indicator positioning logic to use the actual avatar size directly.

## Expected Behavior

- A 72-pixel avatar size is available and works with all existing Avatar props (status, showBorder, etc.)
- When a 72px avatar displays a status indicator (no border), the indicator is positioned 4px from the right and bottom edges of the avatar container
- When a 72px avatar displays a status indicator with a border enabled, the indicator is positioned 8px from the right and bottom edges
- The size threshold that determines indicator offset (compact vs. spacious) is based on the actual avatar pixel size (≥ 72 → spacious, < 72 → compact)
- The status wrapper element correctly reflects the avatar's actual size rather than a derived intermediate value

## Why This Matters

Tying indicator placement to an indirect internal representation makes the component harder to maintain and extend. Aligning the positioning logic with the actual avatar sizes makes the behavior predictable and ensures that any new size variants are handled correctly without requiring knowledge of a separate mapping.
