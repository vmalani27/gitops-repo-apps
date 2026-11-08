# GitOps Apps Repository

Declarative GitOps repository for the local engineering cluster. Managed continuously by ArgoCD using the **App of Apps** pattern.

📖 **Complete Architecture & Learning Guide**: See [GitOps Architecture Guide](docs/gitops-architecture.md) for in-depth documentation on the Root Application, AppProjects, child Applications, self-healing, and hands-on experiments.

---

## Repository Structure

```text
.
├── root-application.yaml     # ArgoCD root App of Apps pointing to apps/
├── docs/                     # Architecture & GitOps learning documentation
│   └── gitops-architecture.md
├── apps/                     # Dedicated ArgoCD Application manifests for components
│   ├── kustomization.yaml
│   ├── argocd.yaml
│   ├── demo-app.yaml
│   ├── ingress-nginx.yaml
│   ├── postgresql.yaml
│   └── url-shortener.yaml
└── components/               # Self-contained Kubernetes workloads (1 component = 1 namespace)
    ├── argocd/               # ArgoCD self-configuration & ingress
    ├── demo-app/             # Operational Kubernetes demonstration workload (hello-kubernetes)
    ├── ingress-nginx/        # Ingress controller configurations
    ├── postgresql/           # Persistent PostgreSQL StatefulSet
    └── url-shortener/        # Microservice Helm chart & configurations
```

---

## How It Works

1. **Root Application**: `root-application.yaml` is registered with ArgoCD once and watches the `apps/` directory.
2. **Component Applications**: ArgoCD synchronizes `apps/`, provisioning a dedicated ArgoCD `Application` resource for each component.
3. **Independent Lifecycle**: Each service in `components/` is independently managed, reconciled, monitored, and synced by its own dedicated Application.

---

## Demo Workload (`demo-app`)

The `demo-app` is a functional Kubernetes demonstration workload powered by [`paulbouwer/hello-kubernetes`](https://github.com/paulbouwer/hello-kubernetes).

Accessible at: `http://demo.links.test`

It serves as a live testbed to demonstrate core Kubernetes and GitOps behaviors:
* **Live Pod Metadata**: Reports active Pod Name, Pod IP, and Node Name in real-time.
* **Replica Scaling**: Change `replicas: 1` to `replicas: 3` in `components/demo-app/manifests.yaml`, commit to Git, and observe round-robin load balancing across different pod hostnames.
* **Rolling Updates**: Update the `MESSAGE` environment variable in Git, push, and observe zero-downtime rolling updates in ArgoCD.
* **Health Probes**: Configured with real `livenessProbe` and `readinessProbe` endpoints (`:8080/`).
