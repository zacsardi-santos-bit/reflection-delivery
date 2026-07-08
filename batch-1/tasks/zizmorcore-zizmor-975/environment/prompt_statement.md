I'm working on the plain-text output of zizmor, our GitHub Actions security linter, and there's a gap I keep hitting when triaging results. Right now every finding looks identical in the output even when some of them already have an automatic fix implemented, so people can't tell at a glance which issues they'd have to resolve by hand versus which ones the tool could just patch for them. That slows down remediation a lot.

What I want is for the human-readable rendering to annotate each fixable finding with a clear note saying an auto-fix is available, showing up as part of that finding's annotations in the plain-text diagnostic output. So if a rule already ships a fix implementation, its findings should carry that note; rules without a fix shouldn't get annotated at all.

Also the summary line at the end of a run should include a count of how many findings are fixable, similar to how suppressed findings already get counted. So if say 2 out of 3 findings are fixable, that fixable count should appear alongside the existing suppressed count. And rules that don't have automatic fixes available shouldn't be counted toward that fixable total, they just render like normal.

Basically I want the output to reflect which findings have auto-fixes so teams can prioritize the manual work faster. Can you wire this up?
