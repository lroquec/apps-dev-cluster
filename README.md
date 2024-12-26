# Argo CD Application Configuration

This document describes the configuration of an Argo CD `Application` resource for deploying the `usermgm_app` application using a GitOps approach.

## Overview

The `Application` resource defines how Argo CD should deploy and manage the `usermgm_app` application in the Kubernetes cluster. It includes repository information, synchronization policies, and optional annotations for integration with the Argo CD Image Updater.

## Resource Details

### Kind
- **Kind**: `Application`
- **API Version**: `argoproj.io/v1alpha1`

### Metadata
- **Name**: `usermgm_app`
- **Namespace**: `argocd`
- **Finalizers**: Ensures resources are cleaned up when the application is deleted.

### Annotations (Optional)
- **Image List**: Used by Argo CD Image Updater to identify container images for updates.
- **Update Strategy**: Defines how image updates are applied (e.g., `newest-build`).

### Spec

#### Project
- **Project**: `default`
  - Specifies the Argo CD project the application belongs to.

#### Source
- **Repository URL**: `https://github.com/lroquec/devops-simple-k8s.git`
  - The Git repository containing the application's Kubernetes manifests.
- **Path**: `.`
  - Path to the manifests within the repository.
- **Target Revision**: `HEAD`
  - Specifies the branch or commit to deploy from the repository.

#### Destination
- **Server**: `https://kubernetes.default.svc`
  - The Kubernetes cluster where the application will be deployed.

#### Synchronization Policy
- **Automated Synchronization**:
  - **Prune**: Automatically removes resources no longer defined in the repository.
  - **Self-Heal**: Automatically restores resources to the desired state if they drift.
  - **Allow Empty**: Ensures the sync does not fail when there are no resources in the manifest.
- **Sync Options**:
  - `Validate=true`: Validates resources before applying.
  - `CreateNamespace=true`: Automatically creates the namespace if it does not exist.
  - `PrunePropagationPolicy=foreground`: Ensures resources are cleaned up in a cascading manner.
  - `PruneLast=true`: Deletes resources in the correct order to avoid dependency issues.

## Usage

1. Apply the `application` manifest to your Kubernetes cluster:
   ```bash
   kubectl apply -f usermgm_app.yaml
   ```
2. Verify the `application` is created in Argo CD:
   ```bash
   kubectl get applications -n argocd
   ```
3. Monitor the application's sync status using the Argo CD UI or CLI:
   ```bash
   argocd app get usermgm_app
   ```

## Notes

- Ensure Argo CD is installed and configured in your Kubernetes cluster.
- If using the optional annotations, configure the Argo CD Image Updater to manage image updates.
- Verify that the repository contains valid Kubernetes manifests and that the `argocd` namespace exists.

## Troubleshooting

- Check the application logs if deployment fails:

   ```bash
   kubectl logs -n argocd -l app.kubernetes.io/name=argocd-server
   ```
- Use the Argo CD CLI to inspect the application:
   ```bash
   argocd app list
   argocd app get usermgm_app
   ```