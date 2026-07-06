## Description

The library is preparing for a major version release that will remove some older, convenience-oriented entry points for its UI protocol integration. Before removing these APIs, they need to be formally deprecated so developers receive clear guidance to migrate to the newer, better-organized alternatives.

Currently, there are three older entry points that should emit deprecation warnings:
1. The original module path used to access the UI protocol integration
2. A convenience method on the agent class that creates a ready-to-use app
3. A standalone application wrapper class

None of these currently emit any deprecation warnings, so developers using them have no indication that they will be removed in the next major version.

## Expected Behavior

- Importing the legacy module path should immediately emit a deprecation warning pointing users to the new module location.
- Calling the convenience method on an agent to produce an application should emit a deprecation warning directing users to compose the new adapter directly.
- Constructing the legacy application wrapper class should emit a deprecation warning directing users to the new composition approach.

All three deprecated entry points must remain fully functional during the current major version lifecycle — they should warn, not break.

## Why This Matters

Without these warnings, developers have no signal to start migrating. When the major version drop arrives and these APIs are removed, projects will break silently. Adding deprecation warnings now gives the community time to update their code while everything still works.
