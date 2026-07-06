Implement a fix in the `ToolGroupMessage` component to ensure that when a topic-update tool call appears in the middle of a sequence of regular tool calls, the tools following the topic-update are rendered with a complete bordered box, including the top border. This will maintain a consistent and visually complete UI.

*   Update the `ToolGroupMessage` component in `packages/cli/src/ui/components/messages/ToolGroupMessage.tsx`:
    *   Implement `prevIsTopic` tracking logic similar to `prevIsCompact`.
    *   Modify the `isFirstProp` calculation to use `prevIsTopic || prevIsCompact` instead of `prevIsCompact` alone.
    *   Ensure that both rendering code paths that compute `isFirstProp` incorporate this logic.

*   Verify the changes by updating the vitest snapshot:
    *   Update the snapshot file `packages/cli/src/ui/components/messages/__snapshots__/ToolGroupMessage.test.tsx.snap`.
    *   Ensure the new snapshot key is `<ToolGroupMessage /> > Golden Snapshots > renders update_topic in the middle of other tools > update_topic_middle 1`.
    *   Run `npx vitest run --update-snapshots src/ui/components/messages/ToolGroupMessage.test.tsx` from `packages/cli/` without `CI=true` to generate the updated snapshot.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.