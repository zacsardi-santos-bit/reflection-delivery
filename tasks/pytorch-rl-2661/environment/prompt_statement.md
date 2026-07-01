I'm trying to use the chess environment in this reinforcement learning library, but I'm running into several problems that make it unusable.

First, the reward signals are wrong. When white wins by checkmate, the reward should be +1, and when black wins, it should be -1. Currently it seems to compute the reward incorrectly, mixing up whose turn it is with who actually won. Draw outcomes — including stalemate, insufficient material, and the fifty-move rule — should all give a reward of 0, but this doesn't seem to be working reliably either. The fifty-move rule also doesn't appear to trigger the done flag at all.

Second, I need a way to get the list of legal moves for a given board position. Right now there's no public method for this. I want to be able to call something on the environment to get a list of moves in standard algebraic notation so I can look up a move by name and use its index as the action.

Third, when I try to reset the environment to a specific position using a position string, if that position is already a finished game (checkmate, stalemate, etc.), the environment should refuse and raise a clear error explaining that you can't reset to a game-over state. Right now it just accepts the position silently.

Could you fix these issues in the chess environment? The environment should support both stateful and stateless modes, allow resetting to arbitrary positions, correctly detect all terminal conditions, and return accurate rewards based on the actual game outcome.
