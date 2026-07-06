Implement a chess environment for reinforcement learning that supports standard gameplay mechanics, correct reward signals, and the ability to start from arbitrary board positions. Ensure the environment correctly handles terminal conditions and provides a method to list legal moves.

*   Ensure `ChessEnv` is importable from `torchrl.envs` and passes the standard environment spec validation check.
*   Implement `ChessEnv` with a boolean `stateful` parameter, ensuring `env.rollout()` completes without error in both modes.
*   Expose a `lib` attribute in `ChessEnv` referencing the chess Python module, allowing access to `env.lib.WHITE` and `env.lib.BLACK`.
*   Implement `get_legal_moves(self, tensordict=None, uci=False) -> List[str]`:
    *   Return legal moves in SAN format by default; use UCI format if `uci=True`.
    *   For stateful environments, use the current board state if `tensordict` is None.
    *   For stateless environments, require `tensordict` with a valid FEN string.
*   Implement `_reset(self, tensordict=None) -> TensorDict`:
    *   Reset the board to a given FEN position and return a TensorDict with 'fen', 'turn', and 'done' keys.
    *   Raise a `ValueError` with "Cannot reset to a fen that is a gameover state" if the FEN represents a terminal state.
*   Implement `_step(self, tensordict) -> TensorDict`:
    *   Execute a move using the "action" index from the legal move list.
    *   Return a TensorDict containing 'done', 'reward', and 'turn' keys.
    *   Set 'reward' to +1 for white checkmate, -1 for black checkmate, and 0 for draws or non-terminal moves.
    *   Set 'done' to True for terminal states and False otherwise.
    *   Flip 'turn' from the previous state.

*   Ensure `env.reset(TensorDict({'fen': fen_string}))` correctly sets the board and identifies terminal positions.
*   Ensure `env.step(td)` updates 'next' TensorDict with accurate 'reward', 'done', and 'turn' values.
*   Implement action space using integer indices into the list of legal moves.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.