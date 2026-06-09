# Argo CD Bootstrap

Gate 3 starts GitOps by installing Argo CD into the existing GKE cluster and applying the root Application.

## Bootstrap Scope

The bootstrap keeps Argo CD internal at first. No public ingress or TLS endpoint is created in this step. Access for verification is done with port-forwarding or direct Kubernetes API access.

## Manual Bootstrap Commands

Run these commands from a workstation with `kubectl` access to the `inenp-dev-gke` cluster:

```bash
kubectl apply -f infrastructure/argocd/namespace.yaml
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl apply -f infrastructure/argocd/root-application.yaml
```

## Verification

```bash
kubectl -n argocd get pods
kubectl -n argocd get applications.argoproj.io
```

The root Application points to `apps/platform` on the `main` branch and automatically syncs child Applications that are added there.

## Follow-up

- Add operator child Applications in separate pull requests.
- Configure public Argo CD access only after cert-manager and DNS automation are stable.
