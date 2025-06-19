# Kustomize Workshop - DRY ArgoCD App-of-Apps Pattern

This project demonstrates a DRY (Don't Repeat Yourself) approach to managing ArgoCD applications using the app-of-apps pattern with Kustomize.

## Structure Overview

- **components/templates/**: Contains reusable templates for ArgoCD Applications
  - **argocd/**: Templates for Applications and App-of-Apps Applications

- **app-of-apps/**: Contains all application definitions for ArgoCD
  - **base/**: Base configurations for application types
    - **argocd-app-of-apps/**: Base configuration for ArgoCD app-of-apps
    - **ingress-nginx-app-of-apps/**: Base configuration for Ingress NGINX app-of-apps
    - **microservices-app-of-apps/**: Base configuration for Microservices app-of-apps
    - **argocd/**: Base configuration for ArgoCD applications
    - **ingress-nginx/**: Base configuration for Ingress NGINX applications
    - **microservices/**: Base configuration for Microservice applications
  - **overlays/**: Environment-specific configurations
    - **dev/shop/**: Development environment overlay for shop cluster
    - **qa/shop/**: QA environment overlay for shop cluster

- **bootstrap/**: Entry point for cluster deployment
  - **base/shop/**: Base configurations for the shop cluster
  - **overlays/**: Environment-specific deployment configurations
    - **dev/shop/**: Development environment overlay for shop cluster
    - **qa/shop/**: QA environment overlay for shop cluster

## How to Use

1. Apply the bootstrap configuration for a specific environment:

```bash
kubectl apply -k bootstrap/overlays/dev/shop
```

2. ArgoCD will then automatically deploy all the applications defined in the app-of-apps structure.

## Adding New Applications

1. Add the application component under `components/`
2. Create a base kustomization for the application under `app-of-apps/base/{app-type}/{app-name}/`
3. Reference the new application in the relevant app-of-apps kustomization file

## Adding New Environments or Clusters

1. Create a new overlay directory under `app-of-apps/overlays/{env}/{cluster}/`
2. Create a new overlay directory under `bootstrap/overlays/{env}/{cluster}/`
3. Reference the appropriate base configurations and make environment-specific customizations

## Benefits of this Structure

- Reusable templates reduce duplication across applications
- Common configurations are defined once in the base layer
- Environment-specific configurations are isolated in overlays
- Easy to add new applications, environments, or clusters
- Maintains a consistent structure across all deployments
