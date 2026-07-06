## Description

Firebase supports AI features that allow developers to intercept content generation calls with custom cloud functions — running logic before or after an AI model processes a request. However, the Firebase CLI currently has no mechanism to deploy or manage these AI-related blocking triggers as part of a standard deployment workflow.

We need to add support for registering, updating, and removing these AI content generation blocking triggers through the CLI. Additionally, we need validation logic to prevent developers from accidentally deploying duplicate triggers for the same event type and scope (e.g., two global hooks for the same event), which would cause deployment failures or unpredictable behavior.

## Expected Behavior

- When deploying a function with an AI content generation blocking trigger, the CLI should register that trigger with the backend API (creating it if it doesn't exist, updating it if it does).
- When removing such a function, the CLI should clean up the corresponding trigger.
- If a developer configures more than one function for the same AI event type in the same scope (both global, or both regional in the same region), deployment should fail with a clear error message.
- Multiple functions for the same event type are allowed if they are in different regions or if one is global and the other is regional.
- Functions for different AI event types (e.g., one for before-generate and one for after-generate) can coexist without conflict.

## Why This Matters

Without this support, developers who want to use Firebase's AI blocking trigger functionality must manually manage trigger registration outside the normal deploy flow, which is error-prone and inconsistent with the rest of the Firebase CLI experience.
