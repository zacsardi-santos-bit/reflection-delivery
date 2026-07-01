## Description

The workload metadata store currently supports tracking containers, pods, and nodes from Kubernetes, but it has no support for Kubernetes Deployment resources. As part of language detection and unified service monitoring, we need the store to also track Deployment-level metadata — specifically the environment/service/version tags and the detected programming languages for each container in a deployment.

## Expected Behavior

- The metadata store should be able to store and retrieve Kubernetes Deployment entities, including their environment, service, version labels and per-container language annotations.
- A retrieval method should be available on the store so callers can look up a specific deployment by its name and get a not-found error if it doesn't exist.
- The Kubernetes API collector should be able to watch Deployment resources and convert them into store entities, parsing both unified service monitoring labels and language detection annotations.
- Language annotations on deployments should support a comma-separated list of language names per container, with whitespace trimming.
- Both regular container languages and init container languages should be tracked separately.
- Deployments that carry no meaningful metadata (no environment tag and no language annotations) should be filtered out rather than stored.
- Which resource types are collected should be configurable: deployment collection should be enabled separately via a language detection configuration flag, while pod collection remains gated on the existing Kubernetes tags configuration flag.
- Node collection should always be active regardless of configuration.

## Why This Matters

Language detection in the Datadog Agent works at the Deployment level — annotations are placed on Deployments to describe what languages their containers run. Without the ability to store and retrieve Deployment metadata, the language detection pipeline cannot propagate this information downstream. This change makes Deployment metadata a first-class entity in the workload metadata store, enabling language detection and unified service monitoring to function correctly in cluster agent scenarios.
