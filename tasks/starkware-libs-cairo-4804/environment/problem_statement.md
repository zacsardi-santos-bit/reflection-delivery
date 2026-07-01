## Description

The Cairo compiler's lowering pipeline runs two optimization passes in the wrong order: block reorganization runs before redundant remapping elimination. This means the block reorganizer often encounters passthrough blocks whose incoming edges still carry variable remappings — and because of that, it refuses to merge them. The result is bloated intermediate representation containing long chains of trivial empty blocks that just jump to the next block, with no real work done.

## Expected Behavior

- The remapping elimination pass should run before the block reorganization pass.
- Once remappings have been cleaned up, the block reorganizer should be able to unconditionally merge any block reachable through a single unconditional jump, without needing to check whether variable remappings are present on that edge.
- The block reorganizer's internal guard that prevents merging blocks with non-empty incoming remappings should be removed; callers are responsible for ensuring remappings are eliminated first.
- After the fix, the lowered IR for functions involving pattern matching on optional values should be significantly more compact — long chains of empty passthrough blocks should collapse into the blocks they forwarded to.

## Why This Matters

Leaving these trivial passthrough blocks in the IR before the match optimizer runs degrades the optimizer's effectiveness. The match optimizer expects reasonably compact IR and benefits from having fewer redundant intermediate blocks to reason about. Fixing the pass order is a prerequisite for clean match optimization output.
