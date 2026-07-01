I'm running into two problems with the linter rules that flag unnecessary else/elif blocks after early exits like returns, raises, continues, and breaks.

The first issue is a crash: if I have code where an else block's body starts with a backslash line continuation character, the linter panics with an arithmetic overflow instead of giving me a warning. This completely breaks linting for any file that contains that syntax. The right behavior would be to still report the diagnostic for that else block, but skip the automatic fix since the backslash makes safe reformatting tricky.

The second issue is that the automatic fix for this whole family of rules is only available when I enable preview mode. Most of our projects don't use preview mode, so we get the warnings but can't get the linter to automatically correct them. These fixes seem mature enough that they should just work out of the box without needing to opt into anything experimental.

Can you fix both of these? The crash should be resolved gracefully, and the auto-fix should be promoted out of preview mode so it's available in standard configuration.
