I maintain Bamboo CI/CD pipeline definitions as YAML files and rely on the bamboo-spec JSON schema for validation and editor autocomplete.

*   The bamboo-spec JSON schema at src/schemas/json/bamboo-spec.json must successfully validate (without errors) each of the 13 YAML fixture documents when checked with a JSON Schema validator using strict:false mode.

*   The schema must accept an optional top-level string property 'server-name' alongside the existing top-level structure, as used in plan-permissions, project-permissions, and deployment project documents.

*   The schema must accept optional boolean properties 'enabled' and 'rerunnable' within the 'plan' object definition.

*   The schema must accept a 'repositories' top-level array containing entries of types: git (with 'url', 'branch', 'shared-credentials', 'ssh-key', 'ssh-key-passphrase', 'change-detection', and various boolean/integer flags), bitbucket (with 'slug'), github (with 'repository', 'user', 'password'), bitbucket-server (with 'server', 'project', 'slug', 'clone-url', 'public-key', 'private-key'), and subversion (with 'plugin-key', 'server-config', 'branch-config').

*   The git repository 'change-detection' object must support a 'quiet-period' sub-object containing 'quiet-period-seconds' (integer) and 'max-retries' (integer) properties, in addition to existing 'exclude-changeset-pattern', 'file-filter-type', and 'file-filter-pattern' properties.

*   Polling trigger entries must accept optional 'repositories' (array of strings) and 'conditions' (array of condition objects) sub-properties alongside the existing 'period' and 'cron' options.

*   The schema must accept a top-level 'plan-permissions' section containing an array of objects, each with optional 'users' (array), 'groups' (array), 'roles' (array), and required 'permissions' (array) properties.

*   The schema must accept top-level 'deployment-permissions', 'default-environment-permissions', and 'environment-permissions' sections for deployment permission configuration.

*   The schema must accept a 'dependencies' top-level object with 'require-all-stages-passing' (boolean), 'enabled-for-branches' (boolean), 'block-strategy' (string), and 'plans' (array of strings) properties.

*   The schema must accept a 'branch-overrides' top-level array and an 'other' top-level object (with freeform additional properties) in plan configurations.

*   Job definitions must accept an 'artifact-subscriptions' property that can be either an empty array or an array of objects containing 'artifact' (string) and optional 'destination' (string) properties.

*   The schema must support all the following task types within job 'tasks' and 'final-tasks' arrays: 'script', 'maven', 'inject-variables' (as either a plain string shorthand or an object with 'file', 'scope', 'namespace', 'conditions'), 'any-task' (with 'plugin-key' and 'configuration'), 'vcs-branch' (with 'branch'), 'vcs-tag' (with 'tag'; and optionally 'repository' and 'working-dir' in deployment environments), 'vcs-commit' (with 'message'), 'vcs-push' (standalone or with 'repository' and 'working-dir'), 'clean', 'test-parser' (as either a plain string shorthand or an object with 'type', 'test-results', and optional 'ignore-time'), 'artifact-download' (with optional 'destination', 'name', 'artifacts' array), and 'checkout' (with 'repository', 'path', 'force-clean-build').

*   The schema must accept a 'deployment' top-level object (with 'name' and optional 'source-plan'), 'release-naming' object, 'environments' array, and named environment definitions at the top level with 'tasks', 'final-tasks', 'docker', 'variables', 'triggers', 'notifications', and 'requirements' properties.

*   The schema must accept the full set of notification event types including 'plan-failed' (with optional numeric shorthand or 'failures' object), 'plan-completed', 'plan-status-changed', 'plan-comment-added', 'plan-responsibility-changed', 'job-completed', 'job-status-changed', 'job-failed', 'job-first-failed', 'job-hung', 'job-queue-timeout', 'job-queued-without-capable-agents', 'job-error' (with optional 'first-only'), 'deployment-started-and-finished', 'deployment-failed', 'deployment-finished', as well as notification recipient types 'users', 'emails', 'groups', 'responsible', 'watchers', 'committers'.


*   Interface details: Type: JSON Schema File
Name: bamboo-spec.json
Location: src/schemas/json/bamboo-spec.json
Description: The JSON Schema that validates Bamboo YAML-as-code pipeline definitions. This file must be updated (or created) to accept all the YAML structures present in the 13 new test fixture files. The schema is validated using AJV with strict:false and allErrors:false. The schema must define (or update definitions for) the following top-level properties and sub-structures:

Top-level properties the schema must accept:
- version (number, required)
- server-name (string, optional) — a filter string for the target Bamboo server
- plan (object) — with project-key, key, name, and optional enabled (boolean), rerunnable (boolean)
- docker (object) — with image, volumes (map), use-default-volumes (boolean), docker-run-arguments (array)
- repositories (array) — entries can be plain strings (linked/project repo references) or named objects with a type discriminator field (git, bitbucket, github, bitbucket-server) or plugin-key (subversion)
- triggers (array) — entries can be: polling (integer shorthand, or object with period/cron/repositories/conditions), cron (string shorthand, or object with expression), remote (null/string shorthand, or object with ip)
- notifications (array) — with recipients and events sub-arrays
- variables (object/map)
- branches (object) — with create, delete, integration, link-to-jira
- dependencies (object) — with require-all-stages-passing, enabled-for-branches, block-strategy, plans
- branch-overrides (array)
- other (object with additional properties allowed)
- stages (array)
- plan-permissions (array) — objects with optional users/groups/roles arrays and required permissions array
- deployment (object) — with name and optional source-plan
- release-naming (object) — with next-version-name, applies-to-branches, auto-increment, auto-increment-variables
- environments (array of strings)
- deployment-permissions (array)
- default-environment-permissions (array)
- environment-permissions (array)
- Additional top-level keys represent job names (in plan configs) or environment names (in deployment configs)

Key sub-structures the schema must define:

Git repository change-detection:
- quiet-period (object): quiet-period-seconds (integer), max-retries (integer)
- exclude-changeset-pattern (string)
- file-filter-type (string)
- file-filter-pattern (string)

Polling trigger:
- period (integer)
- cron (string)
- repositories (array of strings)
- conditions (array of condition objects — freeform objects with condition-name keys mapping to condition configurations)

Job/environment definition (named top-level key):
- key (string)
- tasks (array of task objects)
- final-tasks (array of task objects)
- artifacts (array of artifact objects)
- requirements (array)
- docker (object or string shorthand)
- other (object)
- artifact-subscriptions (array of objects with artifact (string) and optional destination (string), or empty array)

Task types (each as a discriminated union entry in a tasks array):
- script: object with optional interpreter (string) and scripts (array of strings), or array of strings shorthand
- maven: object with executable, jdk, goal, tests, environment, working-dir, project-file, use-return-code
- inject-variables: string shorthand (filename) or object with file, scope, namespace, optional conditions array
- any-task: object with plugin-key (string) and configuration (object)
- vcs-branch: object with branch (string)
- vcs-tag: object with tag (string), and optionally repository (string) and working-dir (string)
- vcs-commit: object with message (string)
- vcs-push: null/empty or object with optional repository (string) and working-dir (string)
- clean: null (standalone keyword)
- test-parser: string shorthand (type name) or object with type (string), test-results (string or array of strings), optional ignore-time (boolean)
- artifact-download: object with optional destination (string), name (string), or artifacts array (objects with name and optional destination)
- checkout: object with repository (string), path (string), force-clean-build (boolean)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.