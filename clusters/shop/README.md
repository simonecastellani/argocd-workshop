# Kustomize Components

## Overview

This directory contains overlay configurations for different clusters. These are designed to be consumed as **Components** rather than Resources in your environment kustomizations.

## Why Components vs Resources

In Kustomize, there's an important distinction between `resources` and `components`:

- **Resources**: Only include the rendered manifests from the referenced kustomization
- **Components**: Include both rendered manifests AND metadata like labels, annotations, and transformers

### Key Benefits of Components

1. **Label and Annotation Inheritance**: Components propagate `commonLabels` and `commonAnnotations` to the parent kustomization
2. **Transformer Reuse**: Components allow transformer configurations to be shared across environments
3. **Better Composition**: Components create a more modular structure where common cross-cutting configurations can be defined once

## Examples

### Cluster Overlay as a Component

In a cluster overlay (`clusters/overlays/shop/kustomization.yaml`):

```yaml
apiVersion: kustomize.config.k8s.io/v1alpha1
kind: Component

commonLabels:
  cluster: shop

resources:
- some-resource.yaml
```

### Using the Component in an Environment

In your environment configuration (`envs/dev/shop/kustomization.yaml`):

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

resources:
- ../../../components/microservice1

# Using the cluster overlay as a component
components:
- ../../../clusters/overlays/shop

commonLabels:
  environment: dev
```

The resulting resources will have BOTH `cluster: shop` AND `environment: dev` labels.

### Incorrect Implementation (for comparison)

If you had used resources instead of components:

```yaml
# This would NOT inherit the cluster labels
resources:
- ../../../components/microservice1
- ../../../clusters/overlays/shop
```

The resources would only include the rendered manifests from the cluster overlay, but not the `cluster: shop` labels.

## Best Practices

1. Use components for configuration that should be inherited (labels, annotations, transformers)
2. Use resources for actual Kubernetes resources you want to include
3. Consider component ordering - components are applied in the order listed