# GitOps Apps Repository

Declarative GitOps repository for the local engineering cluster. Managed by ArgoCD using the "App of Apps" pattern.

## Structure

```text
.
├── root-application.yaml     # ArgoCD root App of Apps pointing to this repository
└── components/               # Self-contained Kubernetes components (1 component = 1 namespace)
    ├── argocd/               # ArgoCD self-configuration & ingress
    ├── demo-app/             # Standalone HTTPD demo service
    ├── postgresql/           # Persistent PostgreSQL StatefulSet
    └── url-shortener/        # Microservice Helm chart & configurations
```

## How It Works

ArgoCD monitors the `main` branch of this repository (`https://github.com/vmalani27/gitops-repo-apps.git`). 
Any changes merged here are automatically reconciled and synchronized to the local Minikube cluster.
