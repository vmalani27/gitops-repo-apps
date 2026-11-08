# GitOps Apps Repository

Declarative GitOps repository for the local engineering cluster. Managed by ArgoCD using the "App of Apps" pattern.

## Structure

```text
.
├── root-application.yaml     # ArgoCD root App of Apps pointing to apps/
├── apps/                     # Dedicated ArgoCD Application manifests for all components
│   ├── kustomization.yaml
│   ├── argocd.yaml
│   ├── demo-app.yaml
│   ├── ingress-nginx.yaml
│   ├── postgresql.yaml
│   └── url-shortener.yaml
└── components/               # Self-contained Kubernetes components (1 component = 1 namespace)
    ├── argocd/               # ArgoCD self-configuration & ingress
    ├── demo-app/             # Standalone HTTPD demo service
    ├── ingress-nginx/        # Ingress controller configurations
    ├── postgresql/           # Persistent PostgreSQL StatefulSet
    └── url-shortener/        # Microservice Helm chart & configurations
```

## How It Works

1. **Root Application**: `root-application.yaml` is deployed to ArgoCD and watches the `apps/` directory.
2. **Component Applications**: ArgoCD synchronizes the `apps/` folder, creating an independent ArgoCD `Application` resource for each component.
3. **Independent Lifecycle**: Each component in `components/` is independently managed, reconciled, monitored, and synced by its own dedicated ArgoCD Application.
