## Description

Kubeadm currently stores the container runtime socket (CRI socket) path as an annotation on the Kubernetes node object. When node registration state needs to be retrieved (e.g., during upgrade or re-join scenarios), the code reads this annotation off the node to learn which container runtime socket was used. This approach tightly couples CRI socket information to the Kubernetes API, requiring a live connection to read something that is inherently local to the node.

## Problem

The correct source of truth for the container runtime endpoint on a node is the kubelet's own instance configuration file, which already stores this information on disk. Reading it from a node annotation is redundant and fragile. Additionally, the function that retrieves node registration information currently accepts a single file path, making it difficult to independently locate the kubelet kubeconfig and the kubelet instance configuration file when they live in different directories.

## Expected Behavior

- Functions that retrieve node registration configuration should accept separate directory paths: one for the kubelet kubeconfig directory and one for the instance configuration directory, so each can be located independently.
- The container runtime socket should be read from the local kubelet instance configuration file rather than from a node annotation.
- The mechanism that annotates nodes with the CRI socket path should be removed, since this information is no longer sourced from node annotations.
- When writing kubelet configuration files during an upgrade, the operation should validate that the instance configuration file is present and return an error if it is missing.

## Why This Matters

Removing the dependency on node annotations for CRI socket information makes kubeadm more resilient and accurate: the CRI socket is read from the actual configuration that governs how the kubelet is running, rather than from a potentially stale or missing node annotation. It also simplifies the node registration retrieval flow and makes the upgrade path more predictable by catching a missing instance config early.
