# GitOps Apps Repository

Declarative GitOps repository for the local engineering cluster. Managed continuously by ArgoCD using the **App of Apps** pattern.

Architecture & Learning Guide: See [GitOps Architecture Guide](docs/gitops-architecture.md) for in-depth documentation on the Root Application, AppProjects, child Applications, self-healing, and hands-on experiments.

---

## Architecture: Two-Repository Pattern

* **Platform Repository ([`My-home-Lab-setup`](https://github.com/vmalani27/My-home-Lab-setup))**: Provisions the Kubernetes cluster, Ingress-NGINX controller, wildcard TLS certificates (`mkcert`), and the ArgoCD operator.
* **GitOps Repository (This Repository)**: The single source of truth for all Kubernetes applications and workloads. Once registered, ArgoCD continuously reconciles cluster state against this repository.

---

## Repository Structure

```text
.
├── root-application.yaml     # ArgoCD root App of Apps pointing to apps/
├── docs/                     # Architecture & GitOps learning documentation
│   └── gitops-architecture.md
├── apps/                     # Dedicated ArgoCD Application manifests
│   ├── kustomization.yaml    # Application list
│   └── demo-app.yaml         # Live operational workload
└── components/               # Self-contained Kubernetes workload manifests
    └── demo-app/             # Operational Kubernetes workload (paulbouwer/hello-kubernetes)
```

---

## Quick Start (GitOps Registration)

Once the cluster and ArgoCD are bootstrapped by `My-home-Lab-setup`, register this GitOps repository with a single command:

```bash
kubectl apply -f root-application.yaml
```

ArgoCD will immediately:
1. Reconcile `root-application` against the `apps/` directory on `main`.
2. Discover and create the child `demo-app` Application.
3. Automatically provision the `demo-app` namespace and roll out the workload.

---

<!--
## Application Component Status (Staged Components Commented Out for Now)

| Component | Namespace | Managed Via | Status | Endpoint |
|---|---|---|---|---|
| **`root-application`** | `argocd` | Manual Bootstrap | **Active** | Tracks `apps/` |
| **`demo-app`** | `demo-app` | ArgoCD (`apps/demo-app.yaml`) | **Active** | [http://demo.links.test](http://demo.links.test) |
| **`postgresql`** | `postgresql` | ArgoCD (`apps/postgresql.yaml`) | Staged | Uncomment in `apps/kustomization.yaml` |
| **`url-shortener`** | `url-shortener` | ArgoCD (`apps/url-shortener.yaml`) | Staged | Uncomment in `apps/kustomization.yaml` |
| **`ingress-nginx`** | `ingress-nginx` | Platform Bootstrap | Active | Managed via `My-home-Lab-setup` |
| **`argocd`** | `argocd` | Platform Bootstrap | Active | [https://argo.links.test](https://argo.links.test) |
-->

## Demo Workload (`demo-app`)

The `demo-app` is a functional Kubernetes demonstration workload powered by [`paulbouwer/hello-kubernetes:1.11`](https://github.com/paulbouwer/hello-kubernetes).

Accessible at: `http://demo.links.test`

It serves as a live testbed to demonstrate core Kubernetes and GitOps behaviors:
* **Live Pod Metadata**: Uses the Downward API to report active **Namespace**, **Pod Name**, and **Node Name** in real-time.
* **Replica Scaling**: Change `replicas: 1` to `replicas: 3` in `components/demo-app/manifests.yaml`, commit to Git, and observe round-robin load balancing across different pod hostnames on page refresh.
* **Zero-Downtime Rollouts**: Update the `MESSAGE` environment variable or image tag in Git, push, and observe zero-downtime rolling updates in ArgoCD.
* **Health Probes**: Configured with native `livenessProbe` and `readinessProbe` endpoints (`:8080/`).
