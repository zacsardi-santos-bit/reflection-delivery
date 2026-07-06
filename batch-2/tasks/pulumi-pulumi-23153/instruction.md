I'm working on adding schedule management commands to the Pulumi CLI so that users can create, list, view, edit, and delete scheduled deployment actions for their stacks.

*   ListStackSchedules must send a GET request to /api/stacks/{owner}/{project}/{stack}/deployments/schedules and return the list of ScheduledAction objects from the response; it must return an error on any HTTP error response and return an empty slice for an empty list.

*   GetStackSchedule must send a GET request to /api/stacks/{owner}/{project}/{stack}/deployments/schedules/{scheduleID} and return the ScheduledAction; it must return an error on non-2xx responses including 404.

*   CreateStackSchedule must send a POST request to /api/stacks/{owner}/{project}/{stack}/deployments/schedules with the serialized CreateScheduledDeploymentRequest body and return the created ScheduledAction; it must return an error on non-2xx responses including 400.

*   CreateStackDriftSchedule must send a POST request to /api/stacks/{owner}/{project}/{stack}/deployments/drift/schedules with the serialized CreateScheduledDriftDeploymentRequest body and return the created ScheduledAction.

*   CreateStackTTLSchedule must send a POST request to /api/stacks/{owner}/{project}/{stack}/deployments/ttl/schedules with the serialized CreateScheduledTTLDeploymentRequest body and return the created ScheduledAction.

*   DeleteStackSchedule must send a DELETE request to /api/stacks/{owner}/{project}/{stack}/deployments/schedules/{scheduleID}; it must return an error on non-2xx responses including 404.

*   UpdateStackSchedule must send a POST request to /api/stacks/{owner}/{project}/{stack}/deployments/schedules/{scheduleID} with the serialized CreateScheduledDeploymentRequest body and return the updated ScheduledAction.

*   UpdateStackDriftSchedule must send a POST request to /api/stacks/{owner}/{project}/{stack}/deployments/drift/schedules/{scheduleID} with the body and return the updated ScheduledAction.

*   UpdateStackTTLSchedule must send a POST request to /api/stacks/{owner}/{project}/{stack}/deployments/ttl/schedules/{scheduleID} with the body and return the updated ScheduledAction.

*   runStackScheduleList must accept a count parameter: 0 means return all; a positive count N returns at most the first N schedules; a count larger than the total returns all. It must pass the (possibly truncated) list to renderFn.

*   renderScheduleListTable must output a table with headers ID, TYPE, SETTINGS, SCHEDULE, NEXT RUN, LAST RUN, CREATED. When the schedule list is empty, it must output 'No scheduled actions configured for this stack.'

*   renderScheduleListJSON must output JSON of the form {"schedules": [...]} where each element has fields id, type, settings, schedule, nextRun, lastRun (omitted when absent), and created. When empty, it must output {"schedules": []}.

*   The scheduleSummary type conversion must infer the schedule type from the schedule's Definition and timing fields: a Destroy operation with ScheduleOnce is type 'ttl' with schedule 'Once'; a DetectDrift operation is type 'drift'; other operations are type 'raw' with settings 'pulumi {operation name}' (e.g. 'pulumi refresh', 'pulumi update'). A Destroy TTL schedule with DeleteAfterDestroy=false has settings 'destroy'; with DeleteAfterDestroy=true has settings 'destroy + delete stack'. A DetectDrift schedule has settings 'detect'.

*   renderScheduleGetText must write labeled fields: 'ID:', 'Type:', 'Settings:', 'Schedule:', 'Next run:', 'Last run:', 'Created:'. When LastExecuted is nil, Last run must display '(never)'.

*   renderScheduleGetJSON must write a JSON object with fields id, type, settings, schedule, nextRun, and created.

*   runStackScheduleNew must validate args before calling any client method. For kind=raw: --operation is required (error contains '--operation is required for --kind=raw'); detect-drift is not a valid operation for raw (error contains 'invalid --operation'); --auto-remediate is invalid (error contains '--auto-remediate is only valid for --kind=drift'); --delete-after-destroy is invalid (error contains '--delete-after-destroy is only valid for --kind=ttl'). For kind=drift: --cron is required (error contains '--cron is required for --kind=drift'); --once is invalid (error contains '--once is not valid for --kind=drift'); --operation is invalid (error contains '--operation is not valid for --kind=drift'); --delete-after-destroy is invalid (error contains '--delete-after-destroy is only valid for --kind=ttl'). For kind=ttl: --once is required (error contains '--once is required for --kind=ttl'); --cron is invalid (error contains '--cron is not valid for --kind=ttl'); --operation is invalid (error contains '--operation is not valid for --kind=ttl'); --auto-remediate is invalid (error contains '--auto-remediate is only valid for --kind=drift'). Unknown kind values return error containing 'invalid --kind'.

*   runStackScheduleNew for kind=raw must call CreateStackSchedule with InheritSettings=true, set ScheduleCron or ScheduleOnce based on which flag is provided, and set the Op field from the operation string (update, refresh, destroy).

*   runStackScheduleNew for kind=drift must call CreateStackDriftSchedule with the ScheduleCron and AutoRemediate values.

*   runStackScheduleNew for kind=ttl must call CreateStackTTLSchedule with Timestamp and DeleteAfterDestroy values.

*   runStackScheduleEdit must first call GetStackSchedule; if that fails, return an error containing 'reading stack schedule:' followed by the underlying error message.

*   runStackScheduleEdit must return an error containing 'at least one of' when no flags have their Changed bool set to true.

*   runStackScheduleEdit must return an error containing '--auto-remediate is only valid for drift' if autoRemediateChanged is true for a non-drift schedule; '--delete-after-destroy is only valid for ttl' for non-ttl schedules; '--once is not valid for drift' for drift schedules; '--operation is not valid for drift' for drift schedules; '--cron is not valid for ttl' for ttl schedules; '--operation is not valid for ttl' for ttl schedules; 'invalid --operation' if operation is detect-drift for a raw schedule.

*   runStackScheduleEdit must preserve all existing fields from the fetched schedule for flags whose Changed bool is false, and only override fields whose Changed bool is true. It must call the appropriate Update method (UpdateStackSchedule for raw, UpdateStackDriftSchedule for drift, UpdateStackTTLSchedule for ttl).

*   runStackScheduleRemove must call DeleteStackSchedule and on success write 'Schedule \'{scheduleID}\' has been removed.' to the writer. On error, it must return an error containing 'removing stack schedule:'.


*   Interface details: # Interfaces Required by Tests

---

## HTTP Client Methods (on `Client` struct)

Location: `pkg/backend/httpstate/client/` (new file, e.g. `client_schedule.go`)

These methods must be added to the existing `Client` struct.

---

Type: Method on Client
Name: ListStackSchedules
Location: pkg/backend/httpstate/client/
Signature: ListStackSchedules(ctx context.Context, stackID StackIdentifier) ([]apitype.ScheduledAction, error)
Description: Sends GET /api/stacks/{owner}/{project}/{stack}/deployments/schedules and returns the list of schedules from the Schedules field of the response. Returns an error on any non-2xx response. Returns empty slice for an empty list.

---

Type: Method on Client
Name: GetStackSchedule
Location: pkg/backend/httpstate/client/
Signature: GetStackSchedule(ctx context.Context, stackID StackIdentifier, scheduleID string) (apitype.ScheduledAction, error)
Description: Sends GET /api/stacks/{owner}/{project}/{stack}/deployments/schedules/{scheduleID}. Returns error on non-2xx (e.g. 404).

---

Type: Method on Client
Name: CreateStackSchedule
Location: pkg/backend/httpstate/client/
Signature: CreateStackSchedule(ctx context.Context, stackID StackIdentifier, req apitype.CreateScheduledDeploymentRequest) (apitype.ScheduledAction, error)
Description: Sends POST /api/stacks/{owner}/{project}/{stack}/deployments/schedules with the serialized request body. Returns the created schedule. Returns error on non-2xx (e.g. 400).

---

Type: Method on Client
Name: CreateStackDriftSchedule
Location: pkg/backend/httpstate/client/
Signature: CreateStackDriftSchedule(ctx context.Context, stackID StackIdentifier, req apitype.CreateScheduledDriftDeploymentRequest) (apitype.ScheduledAction, error)
Description: Sends POST /api/stacks/{owner}/{project}/{stack}/deployments/drift/schedules with the serialized request body. Returns the created schedule.

---

Type: Method on Client
Name: CreateStackTTLSchedule
Location: pkg/backend/httpstate/client/
Signature: CreateStackTTLSchedule(ctx context.Context, stackID StackIdentifier, req apitype.CreateScheduledTTLDeploymentRequest) (apitype.ScheduledAction, error)
Description: Sends POST /api/stacks/{owner}/{project}/{stack}/deployments/ttl/schedules with the serialized request body. Returns the created schedule.

---

Type: Method on Client
Name: DeleteStackSchedule
Location: pkg/backend/httpstate/client/
Signature: DeleteStackSchedule(ctx context.Context, stackID StackIdentifier, scheduleID string) error
Description: Sends DELETE /api/stacks/{owner}/{project}/{stack}/deployments/schedules/{scheduleID}. Returns error on non-2xx (e.g. 404).

---

Type: Method on Client
Name: UpdateStackSchedule
Location: pkg/backend/httpstate/client/
Signature: UpdateStackSchedule(ctx context.Context, stackID StackIdentifier, scheduleID string, req apitype.CreateScheduledDeploymentRequest) (apitype.ScheduledAction, error)
Description: Sends POST /api/stacks/{owner}/{project}/{stack}/deployments/schedules/{scheduleID} with the serialized request body. Returns the updated schedule.

---

Type: Method on Client
Name: UpdateStackDriftSchedule
Location: pkg/backend/httpstate/client/
Signature: UpdateStackDriftSchedule(ctx context.Context, stackID StackIdentifier, scheduleID string, req apitype.CreateScheduledDriftDeploymentRequest) (apitype.ScheduledAction, error)
Description: Sends POST /api/stacks/{owner}/{project}/{stack}/deployments/drift/schedules/{scheduleID} with the serialized request body. Returns the updated schedule.

---

Type: Method on Client
Name: UpdateStackTTLSchedule
Location: pkg/backend/httpstate/client/
Signature: UpdateStackTTLSchedule(ctx context.Context, stackID StackIdentifier, scheduleID string, req apitype.CreateScheduledTTLDeploymentRequest) (apitype.ScheduledAction, error)
Description: Sends POST /api/stacks/{owner}/{project}/{stack}/deployments/ttl/schedules/{scheduleID} with the serialized request body. Returns the updated schedule.

---

## API Types

Location: sdk/go/common/apitype/ (new file, e.g. schedules.go)

These types are referenced by the test files and must exist in the apitype package.

---

Type: Struct
Name: ScheduledAction
Location: sdk/go/common/apitype/
Description: Describes a scheduled deployment action returned by the API.
Fields:
  - ID string (json:"id")
  - OrgID string (json:"orgID,omitempty")
  - ScheduleCron string (json:"scheduleCron,omitempty")
  - ScheduleOnce string (json:"scheduleOnce,omitempty")
  - NextExecution string (json:"nextExecution,omitempty")
  - Paused bool (json:"paused")
  - Kind ScheduledActionKind (json:"kind")
  - Definition json.RawMessage (json:"definition,omitempty")
  - Created string (json:"created,omitempty")
  - Modified string (json:"modified,omitempty")
  - LastExecuted *string (json:"lastExecuted,omitempty")

---

Type: TypeAlias
Name: ScheduledActionKind
Location: sdk/go/common/apitype/
Description: String type identifying the kind of scheduled action.
Value: ScheduledActionKindDeployment ScheduledActionKind = "deployment"

---

Type: Struct
Name: ListScheduledActionsResponse
Location: sdk/go/common/apitype/
Description: API response wrapping a list of scheduled actions.
Fields:
  - Schedules []ScheduledAction (json:"schedules")

---

Type: Struct
Name: ScheduledDeploymentDefinition
Location: sdk/go/common/apitype/
Description: The shape of ScheduledAction.Definition when Kind is ScheduledActionKindDeployment.
Fields:
  - ProgramID string (json:"programID,omitempty")
  - Request *CreateDeploymentRequest (json:"request,omitempty")

---

Type: Struct
Name: CreateScheduledDeploymentRequest
Location: sdk/go/common/apitype/
Description: Request payload for creating or updating a raw scheduled deployment.
Fields:
  - ScheduleCron string (json:"scheduleCron,omitempty")
  - ScheduleOnce string (json:"scheduleOnce,omitempty")
  - Request *CreateDeploymentRequest (json:"request,omitempty")

---

Type: Struct
Name: CreateScheduledDriftDeploymentRequest
Location: sdk/go/common/apitype/
Description: Request payload for creating or updating a drift detection schedule. AutoRemediate must always be serialized (no omitempty).
Fields:
  - ScheduleCron string (json:"scheduleCron,omitempty")
  - AutoRemediate bool (json:"autoRemediate") — no omitempty

---

Type: Struct
Name: CreateScheduledTTLDeploymentRequest
Location: sdk/go/common/apitype/
Description: Request payload for creating or updating a TTL destroy schedule. DeleteAfterDestroy must always be serialized (no omitempty).
Fields:
  - Timestamp string (json:"timestamp,omitempty")
  - DeleteAfterDestroy bool (json:"deleteAfterDestroy") — no omitempty

---

## CLI Schedule Commands

Location: pkg/cmd/pulumi/stack/ (new files)

---

Type: Struct
Name: scheduleSummary
Location: pkg/cmd/pulumi/stack/
Description: Flattened human-readable summary of a scheduled action. Used as the JSON output shape for list and get commands. The ID field is exported (capital I) to allow JSON deserialization by tests.
Fields:
  - ID string (exported, json:"id")
  - Type string (json:"type")
  - Settings string (json:"settings")
  - Schedule string (json:"schedule")
  - NextRun string (json:"nextRun,omitempty")
  - LastRun string (json:"lastRun,omitempty")
  - Created string (json:"created,omitempty")

---

Type: Constants
Name: scheduleKindRaw, scheduleKindDrift, scheduleKindTTL
Location: pkg/cmd/pulumi/stack/
Description: String constants identifying schedule kinds.
Values:
  - scheduleKindRaw = "raw"
  - scheduleKindDrift = "drift"
  - scheduleKindTTL = "ttl"

---

Type: Struct
Name: stackScheduleNewArgs
Location: pkg/cmd/pulumi/stack/
Description: Arguments for creating a new schedule.
Fields:
  - kind string (one of scheduleKindRaw, scheduleKindDrift, scheduleKindTTL)
  - cron string
  - once string
  - operation string
  - autoRemediate bool
  - deleteAfterDestroy bool

---

Type: Struct
Name: stackScheduleEditFlags
Location: pkg/cmd/pulumi/stack/
Description: Flags for editing an existing schedule, with a companion Changed bool for each field indicating whether the user explicitly set it.
Fields:
  - cron string
  - cronChanged bool
  - once string
  - onceChanged bool
  - operation string
  - operationChanged bool
  - autoRemediate bool
  - autoRemediateChanged bool
  - deleteAfterDestroy bool
  - deleteAfterDestroyChanged bool

---

Type: TypeAlias
Name: scheduleGetRenderFunc
Location: pkg/cmd/pulumi/stack/
Description: Render function type for single-schedule output (used by get, new, and edit commands).
Signature: func(w io.Writer, schedule apitype.ScheduledAction) error

---

Type: TypeAlias
Name: scheduleListRenderFunc
Location: pkg/cmd/pulumi/stack/
Description: Render function type for multi-schedule output (used by the list command).
Signature: func(w io.Writer, schedules []apitype.ScheduledAction) error

---

Type: Interface
Name: stackScheduleListClient
Location: pkg/cmd/pulumi/stack/
Description: Client interface needed by the list command.
Method: ListStackSchedules(ctx context.Context, id client.StackIdentifier) ([]apitype.ScheduledAction, error)

---

Type: TypeAlias
Name: stackScheduleListClientFactory
Location: pkg/cmd/pulumi/stack/
Description: Factory function type for the list command.
Signature: func(ctx context.Context, stackFlag string) (stackScheduleListClient, client.StackIdentifier, error)

---

Type: Interface
Name: stackScheduleGetClient
Location: pkg/cmd/pulumi/stack/
Description: Client interface needed by the get command.
Method: GetStackSchedule(ctx context.Context, id client.StackIdentifier, scheduleID string) (apitype.ScheduledAction, error)

---

Type: TypeAlias
Name: stackScheduleGetClientFactory
Location: pkg/cmd/pulumi/stack/
Description: Factory function type for the get command.
Signature: func(ctx context.Context, stackFlag string) (stackScheduleGetClient, client.StackIdentifier, error)

---

Type: Interface
Name: stackScheduleNewClient
Location: pkg/cmd/pulumi/stack/
Description: Client interface needed by the create command.
Methods:
  CreateStackSchedule(ctx context.Context, id client.StackIdentifier, req apitype.CreateScheduledDeploymentRequest) (apitype.ScheduledAction, error)
  CreateStackDriftSchedule(ctx context.Context, id client.StackIdentifier, req apitype.CreateScheduledDriftDeploymentRequest) (apitype.ScheduledAction, error)
  CreateStackTTLSchedule(ctx context.Context, id client.StackIdentifier, req apitype.CreateScheduledTTLDeploymentRequest) (apitype.ScheduledAction, error)

---

Type: TypeAlias
Name: stackScheduleNewClientFactory
Location: pkg/cmd/pulumi/stack/
Description: Factory function type for the create command.
Signature: func(ctx context.Context, stackFlag string) (stackScheduleNewClient, client.StackIdentifier, error)

---

Type: Interface
Name: stackScheduleEditClient
Location: pkg/cmd/pulumi/stack/
Description: Client interface needed by the edit command.
Methods:
  GetStackSchedule(ctx context.Context, id client.StackIdentifier, scheduleID string) (apitype.ScheduledAction, error)
  UpdateStackSchedule(ctx context.Context, id client.StackIdentifier, scheduleID string, req apitype.CreateScheduledDeploymentRequest) (apitype.ScheduledAction, error)
  UpdateStackDriftSchedule(ctx context.Context, id client.StackIdentifier, scheduleID string, req apitype.CreateScheduledDriftDeploymentRequest) (apitype.ScheduledAction, error)
  UpdateStackTTLSchedule(ctx context.Context, id client.StackIdentifier, scheduleID string, req apitype.CreateScheduledTTLDeploymentRequest) (apitype.ScheduledAction, error)

---

Type: TypeAlias
Name: stackScheduleEditClientFactory
Location: pkg/cmd/pulumi/stack/
Description: Factory function type for the edit command.
Signature: func(ctx context.Context, stackFlag string) (stackScheduleEditClient, client.StackIdentifier, error)

---

Type: Interface
Name: stackScheduleRemoveClient
Location: pkg/cmd/pulumi/stack/
Description: Client interface needed by the remove command.
Method: DeleteStackSchedule(ctx context.Context, id client.StackIdentifier, scheduleID string) error

---

Type: TypeAlias
Name: stackScheduleRemoveClientFactory
Location: pkg/cmd/pulumi/stack/
Description: Factory function type for the remove command.
Signature: func(ctx context.Context, stackFlag string) (stackScheduleRemoveClient, client.StackIdentifier, error)

---

Type: Function
Name: runStackScheduleList
Location: pkg/cmd/pulumi/stack/
Signature: runStackScheduleList(ctx context.Context, w io.Writer, factory stackScheduleListClientFactory, stackFlag string, count int, render scheduleListRenderFunc) error
Description: Fetches all schedules for the stack. If count > 0 and count < len(schedules), truncates to first count items. Passes the raw []apitype.ScheduledAction to render.

---

Type: Function
Name: renderScheduleListTable
Location: pkg/cmd/pulumi/stack/
Signature: renderScheduleListTable(w io.Writer, schedules []apitype.ScheduledAction) error
Description: Renders schedules as a table with headers: ID, TYPE, SETTINGS, SCHEDULE, NEXT RUN, LAST RUN, CREATED. When schedules is empty, writes "No scheduled actions configured for this stack." and returns nil.

---

Type: Function
Name: renderScheduleListJSON
Location: pkg/cmd/pulumi/stack/
Signature: renderScheduleListJSON(w io.Writer, schedules []apitype.ScheduledAction) error
Description: Converts each schedule to a scheduleSummary and renders JSON {"schedules": [...]}. When empty, renders {"schedules": []}.

---

Type: Function
Name: runStackScheduleGet
Location: pkg/cmd/pulumi/stack/
Signature: runStackScheduleGet(ctx context.Context, w io.Writer, factory stackScheduleGetClientFactory, stackFlag string, scheduleID string, render scheduleGetRenderFunc) error
Description: Fetches a single schedule by ID and calls render with the raw apitype.ScheduledAction.

---

Type: Function
Name: renderScheduleGetText
Location: pkg/cmd/pulumi/stack/
Signature: renderScheduleGetText(w io.Writer, s apitype.ScheduledAction) error
Description: Renders a single schedule as labeled text. Outputs lines: "ID:   ...", "Type:  ...", "Settings:  ...", "Schedule:  ...", "Next run:  ...", "Last run:  ...", "Created:   ...". Shows "(never)" for Last run when there is no last execution timestamp.

---

Type: Function
Name: renderScheduleGetJSON
Location: pkg/cmd/pulumi/stack/
Signature: renderScheduleGetJSON(w io.Writer, s apitype.ScheduledAction) error
Description: Converts the schedule to a scheduleSummary and renders it as JSON with fields: id, type, settings, schedule, nextRun, created.

---

Type: Function
Name: runStackScheduleNew
Location: pkg/cmd/pulumi/stack/
Signature: runStackScheduleNew(ctx context.Context, w io.Writer, factory stackScheduleNewClientFactory, args stackScheduleNewArgs, render scheduleGetRenderFunc) error
Description: Validates args, calls the appropriate create method based on kind, and renders the created schedule.

---

Type: Function
Name: runStackScheduleEdit
Location: pkg/cmd/pulumi/stack/
Signature: runStackScheduleEdit(ctx context.Context, w io.Writer, factory stackScheduleEditClientFactory, stackFlag string, scheduleID string, flags stackScheduleEditFlags, render scheduleGetRenderFunc) error
Description: Validates that at least one flag is changed, fetches the existing schedule, applies only the changed flags, calls the appropriate update method, and renders the result.

---

Type: Function
Name: runStackScheduleRemove
Location: pkg/cmd/pulumi/stack/
Signature: runStackScheduleRemove(ctx context.Context, w io.Writer, factory stackScheduleRemoveClientFactory, stackFlag string, scheduleID string, yes bool) error
Description: Deletes a schedule by ID. On success, writes "Schedule '{scheduleID}' has been removed." On error, returns an error containing "removing stack schedule:".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.