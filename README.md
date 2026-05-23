# Kubernetes Take-Home Assessment

## Overview

This repository demonstrates a GitOps-friendly Kubernetes deployment structure using Kustomize with separate overlays for staging and production environments.

The application deployed is:
- Image: nginx
- Port: 80
- Secrets injected via Kubernetes Secret references

---

# Project Structure

```text
k8s/
├── base/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── sealedsecret.yaml
│   └── kustomization.yaml
│
└── overlays/
    ├── staging/
    │   ├── namespace.yaml
    │   ├── replica-patch.yaml
    │   ├── image-patch.yaml
    │   └── kustomization.yaml
    │
    └── production/
        ├── namespace.yaml
        ├── replica-patch.yaml
        ├── image-patch.yaml
        └── kustomization.yaml
```

---

# Deployment Commands

## Staging

```bash
kubectl apply -k k8s/overlays/staging
```

## Production

```bash
kubectl apply -k k8s/overlays/production
```

---

# Secret Management Approach

This project uses Bitnami Sealed Secrets.

Why:
- Safe for public repositories
- GitOps-friendly
- Secrets remain encrypted in Git
- Common production approach

Workflow:
1. Create Kubernetes Secret locally
2. Encrypt using kubeseal
3. Commit only SealedSecret manifest

Example:

```bash
kubectl create secret generic webapp-secret \
  --from-literal=DB_PASSWORD=password \
  --from-literal=API_KEY=mykey \
  --dry-run=client -o yaml > secret.yaml
```

Then:

```bash
kubeseal -f secret.yaml -w sealedsecret.yaml
```

---

# Assumptions

- Kubernetes cluster already exists
- Sealed Secrets controller installed
- kubectl and kustomize available locally

---

# Trade-offs

- No ingress configured
- No autoscaling configured
- Focus kept on GitOps structure and secrets

---

# Improvements With More Time

- Add ArgoCD
- Add HPA autoscaling
- Add NetworkPolicies
- Add CI/CD pipeline
- Add monitoring with Prometheus/Grafana
- Add External Secrets Operator integration
