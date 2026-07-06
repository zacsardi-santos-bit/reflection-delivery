## Description

When the security review component renders a transcript of recent conversation history for evaluation, it can drop the most recent tool activity if the user's messages are very large. This means the security reviewer may not see the last shell command that was run or its output — precisely the evidence most relevant to making an approval decision.

In addition, when the formatted representation of a pending action contains content that had to be truncated, the current output does not indicate how much was omitted. Reviewers have no way of knowing whether a small amount was cut or most of the content is missing.

## Expected Behavior

- Recent tool activity (the last shell command invoked and its result) must always appear in the rendered transcript, even when the user conversation history is large enough to fill the entire token budget on its own.
- When content in a formatted action output is truncated, the truncation point should be annotated with the approximate number of tokens that were omitted, so reviewers can gauge the extent of the omission.
- When the transcript rendering drops any entries to stay within the token limit, a clear notice should be returned indicating that some entries were omitted.

## Why This Matters

Security reviewers depend on seeing what the agent actually did (the tool calls and their results) to make informed approval decisions. Losing that context because a user sent many large messages undermines the reliability of the review process. Similarly, knowing how much content was truncated helps developers debug and audit the review pipeline.
