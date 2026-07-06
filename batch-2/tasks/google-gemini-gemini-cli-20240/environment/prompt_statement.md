I'm working on the automatic model selection logic and I want it to actually pay attention to what phase of the planning workflow I'm in instead of just using one model for everything. Right now when I've got an auto model configured it doesn't care whether I'm building a plan or executing one, same model the whole time, which is a waste.

What I want is phase-aware routing. When I'm in plan mode (so the approval mode is set to the planning variant) I want it to route to the high-reasoning Pro variant of whatever auto model family I've got configured, since that's when architectural quality matters most and I want the best possible plan out of it. Then once I've approved the plan and dropped out of plan mode, the tool should notice that an approved plan exists and switch over to the faster Flash variant so execution feels responsive.

Couple of important constraints. This only kicks in when I've actually got an automatic model configured, so if someone's explicitly picked a specific non-auto model, leave them completely alone, don't override anything. Also I want this on by default but there needs to be a settings toggle to turn it off for folks who'd rather have consistent model behavior across phases.

Oh and while you're in there, the routing telemetry events should include the current approval mode too, so when we're staring at diagnostics later it's obvious why a particular model got picked for a given request. That context matters a lot when something looks off.

The whole point is that people using the planning workflow get better plan quality up front without paying for it in speed during implementation, and power users still keep full control.
