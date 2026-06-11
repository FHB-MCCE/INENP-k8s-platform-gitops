# Tenant Isolation Policies

TenantApplication claims create baseline security and resource controls in every
tenant namespace. The controls are managed by Crossplane so new tenants get the
same defaults without copying manifests.

## Resource Boundaries

Each tenant namespace receives a `ResourceQuota` named `tenant-resource-quota`:

```yaml
requests.cpu: "1"
requests.memory: 2Gi
limits.cpu: "2"
limits.memory: 4Gi
pods: "20"
services: "10"
secrets: "20"
configmaps: "20"
persistentvolumeclaims: "4"
```

Each namespace also receives a `LimitRange` named `tenant-default-limits`. It sets
container defaults to `100m` CPU and `128Mi` memory requests, `500m` CPU and
`512Mi` memory limits, and caps a single container at `1` CPU and `1Gi` memory.

## Network Boundaries

The `tenant-ingress-isolation` NetworkPolicy selects all pods in the tenant
namespace. It allows ingress only from:

- pods in the same tenant namespace
- the `ingress-nginx` namespace

This prevents pods from other tenant namespaces from directly calling tenant
workloads. Egress remains open so the backend can reach required external APIs and
cluster services; stricter egress can be added later once the runtime dependency
set is fully fixed.

## Secret Boundaries

Secrets are referenced by name only. The composition creates ExternalSecret
objects that let External Secrets Operator materialize Kubernetes Secrets from
Google Secret Manager:

- `backend-app-secrets` for database and AVWX backend values
- `frontend-ghcr-pull` for the frontend GHCR image pull secret

No plaintext secret values or rendered `.dockerconfigjson` payloads are committed
to Git. Tenant application manifests only reference the resulting Kubernetes
Secret names inside their namespace.

## RBAC Boundary

The composition does not grant tenant workloads broad Kubernetes RBAC. Tenant
workloads use chart-provided service accounts in their own namespace. Cluster-wide
write access remains with the platform controllers: Crossplane, Argo CD, and
External Secrets Operator.

## Verification Scope

Runtime verification of Argo CD sync, ESO synchronization, image pull behavior,
NetworkPolicies, ResourceQuotas, and LimitRanges is tracked in issue #18.
