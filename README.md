# GitOps Project

This repository contains a GitOps workflow implementation for Kubernetes using ArgoCD. It follows the modern GitOps pattern where the Git repository is the source of truth for declarative infrastructure and applications.

## Project Overview

This project implements a GitOps approach for managing multiple environments (dev, test) and clusters (hq, shop) using Argo CD. The repository structure follows the [GitOps folder structure recommendations](https://github.com/gnunn-gitops/standards/blob/master/folders.md) with some customizations.

## Directory Structure

```
.
├── bootstrap/              # Bootstrap configurations for ArgoCD setup
├── clusters/               # Cluster-specific configurations
├── components/             # Reusable application components
├── envs/                   # Environment-specific configurations
└── helm-charts/            # Helm charts for deployment
```

### Detailed Description

#### Bootstrap

The `bootstrap` directory contains configurations needed to initialize ArgoCD in a new cluster. It uses a Helm chart to deploy an "app-of-app" pattern that manages all other applications.

- `base/`: Contains base configuration for the bootstrap process
- `shop/overlays/`: Environment-specific bootstrap configurations for the shop cluster

#### Clusters

The `clusters` directory contains cluster-specific configurations:

- `hq/`: Configurations specific to the headquarters cluster
- `shop/`: Configurations specific to the shop cluster

Each cluster folder follows the overlay pattern with:
- `overlays/dev/`: Development environment overlay
- `overlays/test/`: Test environment overlay

#### Components

The `components` directory contains reusable Kubernetes resources organized by application:

- `argocd/`: ArgoCD installation manifests and configuration
- `ingress-nginx/`: Ingress controller configuration
- `microservice1/` & `microservice2/`: Individual microservice components with their respective Kubernetes resources

#### Environments (envs)

The `envs` directory contains environment-specific configurations:

- `base/`: Base configurations for each cluster
- `overlays/`: Environment-specific overlays (dev, test) for each cluster with environment-specific configurations

#### Helm Charts

The `helm-charts` directory contains Helm charts used in the deployment process:

- `argocd-app-of-app/`: Helm chart for implementing the "app-of-app" pattern in ArgoCD

## GitOps with ArgoCD Approach

This repository implements the GitOps approach using ArgoCD, where:

1. **Git as Single Source of Truth**: All Kubernetes resources, configurations, and application definitions are stored in this Git repository.

2. **Declarative Configuration**: All system configurations are declared as code and versioned in Git.

3. **Automated Synchronization**: ArgoCD monitors this repository and automatically applies changes to the cluster when updates are committed.

4. **App-of-Apps Pattern**: We use the ArgoCD App-of-Apps pattern where a root application (defined in bootstrap) manages the deployment of other applications.

## Relationship Between Clusters, Components, and Environments

The relationship between these three key directories follows this pattern:

1. **Components**: Define the actual application resources (deployments, services, etc.)
2. **Environments**: Customize configurations for different environments (dev, test)
3. **Clusters**: Reference the appropriate environment configurations for each specific cluster

The flow works as follows:

```
Clusters → Environments → Components
```

For example, a cluster configuration in `clusters/shop/overlays/dev/kustomization.yaml` references the corresponding environment configuration in `envs/overlays/dev/shop/kustomization.yaml`, which in turn includes the base components with environment-specific patches.

## Detailed Example: microservice1

`microservice1` demonstrates how the GitOps approach works in this repository:

### Component Definition

In the `components/microservice1/` directory, we define the base Kubernetes resources for the microservice:

- `config.yaml`: Base ConfigMap template
- `deploy.yaml`: Deployment specification
- `service.yaml`: Service definition
- `ingress.yaml`: Ingress routing
- `kustomization.yaml`: Kustomize file that references all these resources

### Environment Configuration

In the `envs/overlays/dev/shop/microservice1/` directory, we have:

- `config.yaml`: Environment-specific configuration that patches the base ConfigMap, providing dev-specific settings

The `envs/overlays/dev/shop/kustomization.yaml` file includes this patch and references the base shop environment configuration.

### Cluster Deployment

The `clusters/shop/overlays/dev/kustomization.yaml` file references the corresponding environment configuration, effectively applying all the environment-specific settings to the shop cluster in the dev environment.

### ArgoCD Integration

When changes are made to any of these files:

1. ArgoCD detects the changes in the repository
2. ArgoCD applies the changes to the appropriate cluster and namespace
3. Kustomize processes the overlay hierarchy to generate the final resources
4. The application is updated with minimal disruption

For `microservice1`, the ArgoCD application picks up the configuration from the clusters directory, processes all the Kustomize overlays, and deploys the resulting configuration to the appropriate namespace in the cluster.

## Getting Started

To use this repository:

1. Install ArgoCD in your Kubernetes cluster
2. Apply the bootstrap configuration using:
   ```
   kubectl apply -k bootstrap/shop/overlays/dev/
   ```
3. ArgoCD will automatically deploy all applications defined in the repository

## Kind Clusters for Local Development

The repository includes configuration files (`kind-dev-shop.yaml` and `kind-test-shop.yaml`) for setting up local development environments using Kind (Kubernetes in Docker).
