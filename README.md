# GitOps Project

This project demonstrates GitOps practices using ArgoCD, Helm, and Kubernetes.

## Project Structure
- `app-of-apps/`: Contains application of applications configurations
- `bootstrap/`: Contains bootstrap configuration for ArgoCD
- `clusters/`: Contains cluster configuration
- `components/`: Contains component definitions
  - `templates/argocd/`: Contains ArgoCD application templates (Helm-based)
- `envs/`: Contains environment-specific configurations

## Helm-based ArgoCD Application Templates
This project uses Helm charts for ArgoCD application templates. See the [Helm README](./components/templates/argocd/helm-README.md) for details.

## Quick Start
1. Generate the application YAMLs: `./generate-app-yamls.sh`
2. Apply the bootstrap configuration: `kubectl apply -k bootstrap/overlays/dev/shop`