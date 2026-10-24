# Self-Hosted Bare-Metal GitOps Lab (v1 - Predecessor)

> [!NOTE]
> **Architectural Predecessor:** This repository represents **Iteration 1** of my cloud-native platform engineering journey. It served as the proving ground for self-hosted CI/CD (Forgejo / Harbor), Istio Ambient Mesh, and the VictoriaMetrics ecosystem before evolving into [TheOneLab](https://github.com/GiannisStathoudakis/TheOneLab) (v2 — featuring Cilium eBPF, Distributed LGTM, and Distroless supply chains).

A self-hosted, bare-metal Kubernetes platform simulating an enterprise-grade, cloud-native architecture. This project focuses on secure supply chains, centralized identity, deterministic GitOps promotions, and sidecarless service mesh topologies.

---

### Tech Stack
- **Platform & Storage:** RKE2, Rancher, Longhorn (Single-Replica tuned), MetalLB
- **CI/CD & Registry:** Forgejo Actions (isolated DinD sidecar), Kargo, Argo CD, Harbor
- **Security & Identity:** HashiCorp Vault (Internal PKI), External Secrets Operator (ESO), Cert-Manager, Authentik
- **Networking & Mesh:** Istio (Ambient Mode: ztunnel, istio-cni, Gateway API with ReferenceGrant)
- **Observability:** VictoriaMetrics Stack (VictoriaMetrics, VictoriaLogs, VictoriaTraces, vmagent, vlagent), OpenTelemetry Collector (Contrib)

---

### Architecture Highlights
- **Sidecarless Service Mesh (Istio Ambient):** Bootstrapped Istio Ambient Mode utilizing `ztunnel` DaemonSets and `istio-cni` with active DNS capture. 
- **Cross-Namespace Gateway API Routing:** Implemented Kubernetes Gateway API policies using `ReferenceGrant` to securely allow cross-namespace routing between frontend ingress routes and Authentik outposts without relaxing namespace isolation boundaries.
- **Isolated Self-Hosted CI/CD Runners:** Architected a hardened Forgejo runner StatefulSet using a dedicated rootful `docker:dind` sidecar paired with a rootless (`runAsUser: 1000`) orchestrator, avoiding hazardous host socket mounts (`/var/run/docker.sock`). Handled pod startup races via automated tcp socket readiness loops.
- **Zero-Trust Internal Pipelines:** Engineered secure internal webhook communication between Forgejo and Argo CD by navigating SSRF protections and injecting custom Vault-backed Certificate Authorities directly into the Git server's runtime.
- **Deterministic Artifact Promotion:** Enforced strict Semantic Versioning (`v1.0.x`) tied to CI run numbers generated via Google Jib, allowing Kargo's Warehouse to deterministically resolve the absolute newest Freight artifact and eliminate tag collision.
