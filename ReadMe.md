# Self-Hosted Bare-Metal GitOps Lab

A self-hosted, bare-metal Kubernetes environment simulating an enterprise-grade, cloud-native architecture. This project focuses on secure supply chains, centralized identity, and continuous delivery.

### Tech Stack
* **Platform & Storage:** RKE2, Rancher, Longhorn, MetalLB
* **CI/CD & Registry:** Forgejo Actions, Kargo, Argo CD, Harbor
* **Security & Identity:** HashiCorp Vault, External Secrets Operator (ESO), Cert-Manager, Authentik
* **Networking & Mesh:** Istio (Service Mesh & Gateway)
* **Observability:** VictoriaMetrics (Metrics, Logs, Traces), OpenTelemetry, Grafana

### Architecture Highlights
* **Zero-Trust Internal Pipelines:** Engineered secure, internal webhook communication between the Git server and Argo CD by navigating SSRF protections and injecting custom Vault-backed Certificate Authorities directly into the Git server's runtime.
* **Automated State Reconciliation:** Replaced manual deployment triggers with a continuous GitOps reconciliation loop. Kargo's Warehouse actively polls the container registry to discover new immutable artifacts, autonomously packaging them into promotable `Freight` objects and managing the lifecycle progression across environments.
* **Deterministic Artifact Promotion:** Enforced strict Semantic Versioning (SemVer) tied to CI run numbers (e.g., `v1.0.x`) rather than Git SHAs. This ensures Kargo and Argo CD have a mathematically deterministic method of identifying the absolute newest artifact during their polling cycles, eliminating caching errors and false-positive deployments.