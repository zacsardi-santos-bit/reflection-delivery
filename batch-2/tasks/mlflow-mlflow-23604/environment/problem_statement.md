## Description

The experiment-tracking UI needs a complete front-end layer for label schemas — structured definitions that control how human reviewers provide feedback on AI model outputs. Right now the UI has no way to read, create, modify, or delete these schemas through the API, and there are no input controls that can render the appropriate form element (pass/fail toggle, number input, dropdown, or free-text area) for a given schema format.

## Expected Behavior

- A set of data-fetching hooks that talk to the label-schema REST API endpoints for fetching a schema by ID, fetching a schema by experiment and name, listing schemas with pagination, creating a schema, updating a schema (with sparse semantics — only fields that are explicitly provided should be sent to the server), and deleting a schema
- When performing a sparse update, explicitly providing an empty string or boolean false for a field must forward that value to the server rather than treating it as "no change"
- Fetching by ID when no ID is supplied should skip the network request entirely
- Listing schemas should support optional pagination parameters that are omitted from the request when not provided
- A family of input widgets — pass/fail, numeric, categorical (single- and multi-select), and free-text — each rendering the appropriate control and surfacing user input through a change callback
- A dispatcher component that inspects the schema's input definition and renders the correct widget automatically, and shows an error when no recognized input type is present

## Why This Matters

Label schemas are the foundation for structured human evaluation of AI model outputs in MLflow. Without these hooks and widgets, every team building a labeling surface has to independently duplicate the API wiring and form-control logic, leading to inconsistencies and bugs.
