## Description

Airflow currently has no built-in support for executing notebooks on Amazon SageMaker Unified Studio. Teams using this managed environment have to trigger notebook runs, monitor their progress, and relay output values to downstream tasks using custom scripts or external tooling — entirely outside of Airflow's workflow orchestration. This makes it difficult to integrate notebook-based data processing into production pipelines.

## Expected Behavior

- Users should be able to trigger a SageMaker Unified Studio notebook run as a standard Airflow task, supplying parameters such as instance type, run timeout, and arbitrary notebook-level key-value parameters.
- The task should support waiting for notebook completion either synchronously (inline polling) or asynchronously (deferrable mode).
- A dedicated sensor should be available for monitoring an already-started notebook run, checking its status and returning successfully when it completes.
- When a notebook produces output values upon completion, those outputs should be automatically captured and made available to downstream tasks through Airflow's XCom mechanism, enabling chained notebook workflows where one notebook's results feed into another's parameters.
- The operator, sensor, and trigger should correctly handle transient in-progress states (queued, starting, running, stopping) and raise clear errors for terminal failure states.
- A configurable run timeout should be respected, with a sensible default.

## Why This Matters

Without first-class Airflow support, notebook-based workflows on SageMaker Unified Studio cannot be fully integrated into production DAGs. This feature allows data engineering teams to orchestrate multi-step notebook pipelines entirely within Airflow, with proper error handling, retry logic, and inter-task data passing.
