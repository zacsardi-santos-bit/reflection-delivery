## Description

We need a working chess environment for reinforcement learning that supports standard gameplay mechanics, correct reward signals, and the ability to start from arbitrary board positions.

The current chess environment implementation has several issues that make it unusable for training:

1. **Incorrect reward signals**: When a game ends by checkmate, the reward does not correctly distinguish between a white win (+1) and a black win (-1). Draw outcomes (stalemate, the fifty-move rule, and insufficient material) are not all returning 0 as expected.

2. **No ability to list legal moves**: There is no public method to retrieve the list of legal moves for a given board position, making it impossible for users to select actions interactively or inspect the available moves.

3. **No protection against resetting to finished positions**: When a user provides a board position (via FEN notation) that represents a completed game, the environment should raise a clear error. Currently it silently accepts terminal positions, which leads to undefined behavior.

4. **The fifty-move rule is not handled as a terminal condition**: The environment does not detect the fifty-move draw rule as a done state.

## Expected Behavior

- Resetting the environment to a specific board position should return the current side to move and confirm the position is not already finished.
- Attempting to reset to a terminal position (checkmate, stalemate, or draw) should raise a descriptive error.
- A method to enumerate all legal moves for the current position should be available, returning moves in standard algebraic notation by default.
- Reward signals should correctly reflect the game outcome: +1 for white winning, -1 for black winning, and 0 for any draw.
- All terminal conditions (checkmate, stalemate, fifty-move rule, insufficient material) must set the done flag to True.

## Why This Matters

Without these fixes, the chess environment cannot be used effectively for reinforcement learning experiments. Incorrect rewards cause training to diverge, and missing features make the environment unusable as a benchmark.
